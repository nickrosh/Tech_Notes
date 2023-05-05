
TinyURL is a service that converts a URL into a shortened version that users can use instead of the long one, this saves a lot of space:
`https://google.com/something/something/129871040 -> https://tinyurl.com/1fd8h19`

## Requirements

**Functional Requirements**
1. Given a URL, our service should generate a shorter and unique alias of it. This is called a short link. This link should be short enough to be easily copied and pasted into applications
2. When users access a short link, our service should redirect them to the original link.
3. Users should optionally be able to pick a custom short link for their URL.
4. Links will expire after a standard default timespan. Users should be able to specify the expiration time.

**Non-Functional Requirements**
1. The system should be highly available. This is required because, if our service is down, all the URL redirections will start failing.
2. URL redirection should happen in real-time with minimal latency.
3. Shortened links should not be guessable (not predictable)

**Extended Requirements**
1. Analytics; e.g. how many times did redirection happen?
2. Our service should also be accessible through [REST APIs](../../Internet%20&%20Networking/REST%20API.md) by other services.

## Capacity Estimates and Constraints

Our system will be read-heavy. There will be lots of redirection requests compared to new URL shortenings. Let's assume a 100:1 ratio between reads and writes.

**Traffic Estimates**: Assuming, we will have 500M new URL shortenings per month, with 100:1 read/write ratio, we can expect 50B redirections during the same period: $100 * 500M=50B$

What would the Queries Per Second (QPS) be for our system? New URL shortenings per second:
$$500 million / (30 days * 24 hours * 3600 seconds) \approx 200 URLs/sec$$
Considering the 100:1 read/write ratio, URL redirections per second will be:
$$100 * 200 \text{URLs/sec} = 20K/sec$$

**Storage Estimates**: Let's assume we store every URL shortening request (and associated shortened link) for 5 years. Since we expect to have 500M new URLs every month, the total number of objects we expect to store will be 30 billion:
$$500 million * 5 years * 12 months = 30 billion$$
Let's assume that each stored object will be approximately 500 bytes (just a ballpark estimate, we will dig into it later). We will need 15TB of total storage:
$$30billion * 500 bytes = 15TB$$

**Bandwidth Estimates**: For write requests, since we expect 200 new URLs every second, total incoming data for our service will be 100KB per second:
$$200*500bytes = 100KB/s$$
For read requests, since every second we expect ~20K URL redirections, total outgoing data for our service would be 10MB per second:
$$20K * 500 bytes \approx 10MB/s$$


**Memory Estimates**: If we want to [Cache](../../Databases/Database%20Cache.md) some of the hot URLs that are frequently accessed, how much memory will we need to store them? If we follow the 80-20 rule, meaning 20% of URLs generate 80% of traffic, we would like to cache these 20% of hot URLs. Since we have 20K requests per second, we will be getting 1.7 billion requests per day:
$$20K*4600seconds*24hours \approx 1.7billion$$
To cache 20% of these requests, we will need 170GBs of memory:
$$20\% * 1.7billion*500bytes \approx 170GB$$
One thing to note here is that since there will be many duplicate requests (of the same URL), our actual memory usage will be lower than 170GBs.

High Level Estimates: Assuming 500 million new URLs per month and 100:1 read to write ratio, the following is the summary of all of our calculations

| Metric              | Value   |
|:------------------- | ------- |
| New URLs            | 200/s   |
| URL Redirections    | 10K/s   |
| Incoming Data       | 100KB/s |
| Outgoing Data       | 10MB/s  |
| Storage for 5 years | 15TB    |
| Memory for Cache    | 170GB   |


> Once we've finalized the requirements, it's always a good idea to define the system APIs. This should explicitly state what is expected from the system

We can have a REST API to expose the functionality of our service. The following could be the definitions of the APIs for creating and deleting URLs:

```python
def create_url(
	api_dev_key: str,
	original_url: str,
	custom_alias: str = None,
	user_name: str = None,
	expire_date: str = None
) -> str:
	"""
	Args:
		api_dev_key: The API developer key of a registered account. This will be used to, among other things, throttle users based on their allocated quota.
		original_url: Original URL to be shortened
		custom_alias: Optional custom key for the URL
		user_name: Optional user name to be used in the encoding
		expire_date: Optional expiration date for the shortened URL

	Returns: 
		A successful insertion returns the shortened URL, otherwise, it returns an error code.
	"""

def delete_url(api_dev_key: str, url_key: str) -> str:
	"""Where url_key is a string representing the shortened URL to be retrieved, a successful deletion returns "URL removed"
	"""
```

**How do we detect and prevent abuse?** A malicious user can put us out of business by consuming all URL keys in the current design. To prevent abuse, we can limit users via their API key. Each API key can be limited to a certain number of URL creations and redirections per some time period (which may be set to a different duration per developer key).

## Database Design

>Defining the DB schema in the early stages of the interview would help to understand the data flow among various components and later would guide towards data partitioning.

A few observations about the nature of the data we will store:
1. We need to store billions of records
2. Each object we store is small (less than 1K).
3. There are no relationships between records, other than storing which user created a URL.
4. Our service is read-heavy.

**Database Schema:** 
We would need two tables: one for storing information about the URL mappings and one for the user's data who created the short link.

![](../../Attachments/Pasted%20image%2020230504182358.png)

**What kind of database should we use?** Since we anticipate storing billions of rows, and we don't need to use relationships between objects, a [NoSQL](../../Databases/NoSQL.md) store like DynamoDB or Cassandra is a better choice. A NoSQL choice is also easier to scale.

## Basic System Design and Algorithm

The problem we are solving here is how to generate a short and unique key for a given URL. 

#### A. Encoding Actual URL

We can compute a basic hash (e.g. MD5, SHA256, etc.) of the given URL. The hash can then be encoded for display with [Base64](../../Internet%20&%20Networking/Serialization.md). A reasonable question would be, what should the length of the short key be? Using Base64 with 6 characters would result in $64^6=68.7billion$ possible strings. With 68 billion unique strings, let's assume six letter keys would suffice.

If we use the MD5 algorithm as our hash function, it will produce a 128-bit hash value. After Base64 encoding, we'll get a string having more than 21 characters. Now we only have space for 6 or 8 characters per short key. To resolve this, we can choose some other characters out of the encoding string or swap some characters.

What are the different issues with our solution? We have the following couple of problems with our encoding scheme:
1. If multiple users enter the same URL, they can get the same shortened URL, which is not acceptable.
2. What if parts of the URL are URL-encoded?

We can append an increasing sequence of numbers to each input URL before the hash to ensure each is unique. However, this number could overflow or impact performance. Another solution could be to append the User ID to the string, but this requires users to be logged in.

![](../../Attachments/Pasted%20image%2020230504195233.png)

#### B. Generating Keys Offline

We can have a standalone *Key Generation Service* (KGS) that generates random six-letter strings beforehand and stores them in a database (let's call it key-DB). Whenever we want to shorten a URL, we will take one of the already-generated keys and use it. This approach will make things quite simple and fast. Not only are we not encoding the URL, but we won't have to worry about duplications or collisions. KGS will make sure all the keys inserted into key-DB are unique.

**Can concurrency cause problems?** As soon as a key is used, it should be marked in the database to ensure that it is not used again. If there are multiple servers reading keys concurrently, we might get a scenario where two or more servers try to read the same key from the database. How can we solve this [concurrency problem](../Distribution%20Problems.md)? Servers can use KGS to read/mark keys in the database. KGS can use two tables to store keys: one for keys that are not used yet, and one for all the used keys. As soon as KGS gives keys to one of the servers, it can move them to the used keys table. KGS can always keep some keys in memory to quickly provide them whenever a server needs them.

For simplicity, as soon as KGS loads some keys into memory, it can move them to the used keys table. This ensure each server gets unique keys. If KGS dies before assigning all the loaded keys to some server, we will be wasting those keys, which could be acceptable, given the huge number of keys we have. To ensure the same key is not given to multiple servers, KGS must use a read lock on keys when removing it and giving it to a server.

**What would the Key-DB size be?** With base64 encoding, we can generate 68B unique 6 character keys. If we need one byte to store one character, we can store all these keys in 412GBs:
$$6characters *68billion = 412GB$$

**How would we perform a key lookup?** We can look up the key in our database to get the full URL. If it's present in the DB, issue an "[HTTP](../../Internet%20&%20Networking/HTTP.md) 302 Redirect" status back to the browser, passing the stored URL in the "Location" field of the request. If that key is not present in our system, issue  an "HTTP 404 Not Found" status.

**Should we impose size limits on custom aliases?** Our service supports custom aliases. Users can pick any "key" they like, but providing a custom alias is not mandatory. However, it is reasonable (and often desirable) to impose a size limit on a custom alias to ensure we have a consistent URL database. Let's assume users can specify a maximum of 16 characters per customer key (as reflected in the above database schema).

![](../../Attachments/Pasted%20image%2020230504200713.png)


## Data Partitioning and Replication

To scale out our Database, we need to [Partitions](../../Databases/DB%20Partitions.md) it so that it can store information about billions of URLs. There fore, we need to develop a partitioning scheme that would divide and store our data into different DB servers. 

#### Range Based Partitioning

We can store URLs in separate partitions based on the hash key's first letter. This will lead to predictable destinations for keys, and easy querying for a range of adjacent keys if that is needed. The main drawback is that this approach leads to unbalanced servers and hot spots. For example, if we put all keys starting with "E" into one server and then realize the majority of all keys start with "E", the load will be imbalanced.

#### Hash Based Partitioning

We can also take a hash of the object we are storing. We then calculate which partition to user based upon the hash. In our case, we can take the hash of the 'key' or the short link to determine the partition in which we store the data object. Our hash will randomly distribute URLs into different partitions. We can further reduce the risk of overloaded partitions with [Consistent Hashing](../Consistent%20Hashing.md).

## Cache

We can cache URLs that are accessed frequently. We can use any off-the-shelf solution like Redis, which can store full URLs with their respective hashes. Thus, the application servers, before hitting the backend storage, can quickly check if the cache has the desired URL.

How much cache [Memory](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) do we need? We can start with 20% of daily traffic, and as estimated above would need 170GB of memory. Since modern servers can have 256GB of RAM, we can easily fit all the cache into one machine. Alternatively, we can use a couple of smaller servers to store all the cached URLs. 

Which cache eviction policy to user? Least Recently Used (LRU) is reasonable for our system. Under this policy, we discard the least recently accessed object when the cache reaches maximum capacity. To further increase the efficiency, we can [Replicate](../../Databases/DB%20Replication.md) our caching servers to distribute the load between them.

How can each replica be updated? Whenever there is a cache miss, our servers would be hitting a backend database. Whenever this happens, we can update he cache and pass the new entry to all the cache replicas. Each replica can update its cache by adding the new entry. If a replica already has that entry, it can simply ignore it.

![](../../Attachments/Pasted%20image%2020230504202527.png)

## Load Balancer

We can add a [Load Balancer](../Load%20Balancer.md) layer at three places in our system:
1. Between the clients and application servers
2. Between application servers and database servers
3. Between applications servers and cache servers

Initially, we can use a simple round robin approach that distributes incoming requests equally among backend servers. If we want to be more efficient, the Load Balancer can periodically poll the servers about their load and adjust the traffic based on that.

## Purging or DB Cleanup

Should entries stick around forever, or should they be purged? If a user-specific expiration time is reached, what should happen to the link?

If we choose to continuously search for expired links to remove them, it would put a lot of pressure on our database. Instead, we can slowly remove expired links and do a lazy cleanup. Our service will ensure that only expired links will be deleted, although some expired links can live longer but will never be returned to users.
- Whenever a user tries to access an expired link, we can delete the link and return an error to the user. 
- A separate Cleanup service can run periodically to remove expired links from our storage and cache. This service should be very lightweight and scheduled to run only when the user traffic is expected to be low.
- We can have a default expiration time for each link (e.g. two years).
- After removing an expired link, we can put the key back in the key-DB to be reused. 
- Should we remove links that haven't been visited in some length of time, say six months? This could be tricky. Since storage is getting cheap, we can decide to keep links forever.

![](../../Attachments/Pasted%20image%2020230504203305.png)

## Telemetry

How many times was a short URL used, what were the user locations? How would we store these statistics? If it is part of a DB row that gets updated on each view, what will happen when a popular URL is slammed with a large number of concurrent requests? 

Some statistics worth tracking: country of the visitor, date and time of access, web page that referred the click, browser, or platform from where the page was accessed.


## Security and Permissions

Can users create private URLs or allow a particular set of users to access a URL?

We can store the permission level (public/private) with each URL in the database. We can also create a separate table to store User IDs that have permission to see a specific URL. IF a user does not have permission and tries to access a URL, we can send an error (HTTP 401) back. Given that we are storing our data in a NoSQL database like Cassandra, the key for the table storing permissions would be the 'hash' (or KGS generated key). The columns will store the User IDs of those users that have permission to see the URL.