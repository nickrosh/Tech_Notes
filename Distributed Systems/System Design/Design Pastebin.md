
Pastebin-like services enable users to store plain text or images over the network (typically [the Internet](../../Internet%20&%20Networking/TCP-IP%20Model.md)) and generate unique URLs to access the uploaded data. Such services are also used to share data over the network quickly, as users would just need to pass the URL to let other users see it. In our example, users will enter a piece of plain text and get a randomly generated URL to access it.

## Requirements

#### Functional Requirements:
1. Users should be able to upload or "paste" their data and get a unique URL to access it.
2. Users will only be able to upload text.
3. Data and links will expire after a specific timespan automatically; users should also be able to specify expiration time.
4. Users should optionally be able to pick a custom alias for their paste.

#### Non-Functional Requirements:
1. The system should be highly reliable, any data uploaded should not be lost.
2. The system should be highly available. This is required because if our service is down, users will not be able to access their pastes
3. Users should be able to access their pastes in real-time with minimum latency
4. Paste links should not be guessable

#### Extended Requirements:
1. Analytics, e.g. how many times a paste was accessed?
2. Our service should also be accessible through [REST API](../../Internet%20&%20Networking/REST%20API.md) by other services.

## Design Considerations

Pastebin shares some requirements with [URL Shortening Services](Design%20TinyURL.md), but there are some additional design considerations we should keep in mind. 

What should be the limit on the amount of text a user can paste at a time? We can limit users not to have Pastes bigger than 10MBs to stop abuse of the service

Should we impose size limits on custom URLs? Since our service supports custom URLs, users can pick any URL that they like, but providing a custom URL is not mandatory. However, it is reasonable (and often desirable) to impose a size limit on custom URLs, so that we have a consistent database.


## Capacity Estimation and Constraints

Our service will be read-heavy, there will be more read requests compared to new Paste creation. We can assume a 5:1 ratio between reads and writes.

#### Traffic Estimates
Pastebin services are not expected to have traffic similar to [Twitter](Design%20Twitter.md) or Facebook, let's assume here that we get one million new pastes added to our system every day. This leaves us with five million reads per day. New Pastes per day:
$$1 million / (24hours*3600 seconds) \approx 12 pastes/sec$$
Paste reads per second:
$$5million/(24hours*3600seconds) \approx 58 reads/sec$$

#### Storage Estimates
Users can upload a maximum of 10MBs of data. Commonly, Pastebin like services are used to share source code, configs, or logs. Such files are not huge, so let's assume the average Paste size is 10KB. At this rate, we will be storing 10GB of data per day:
$$1million*10KB = 10GB/day$$
If we want to store this data for 10 years, we would need a total storage capacity of 36TB. With 1M pastes every day, we will have 3.6 billion Pastes in 10 years. We need to generate and store keys to uniquely identify these pastes. If we use [base64](../../Internet%20&%20Networking/Serialization.md) encoding, we would need six letter strings ($64^6>68billion$). If it takes one byte to store one character, then the total size required to store 3.6B keys would be:
$$3.6billion*6=22GB$$
22GB is negligible compared to 36TB. To keep some margin, we will assume a 70% capacity model (meaning we don't want to use more than 70% of our total storage capacity at any point), which raises our storage needs to 51.6TB.

#### Bandwidth Estimates
For write requests, we expect 12 new pastes per second, resulting in 120KB of ingress per second:
$$12*10KB = 120KB/s$$
As for the read requests, we expect 58 requests per second. Therefore total data egress (sent to users) will be 600KB/s or 0.6MB/s:
$$58*10KB = 0.6MB/s$$
Although total ingress and egress are not big, we should keep these numbers in mind while designing our service.

#### Memory Estimates
We can cache some of the hot pastes that are frequently accessed. Following the 80-20 rule, meaning 20% of hot pastes generate 80% of the traffic, we would like to cache these 20% pastes. Since we have 5M read requests per day, to cache 20% of these requests, we would need:
$$0.2*5million*10KB \approx 10GB$$


## System APIs

The following could be definitions of the APIs to create/retrieve/delete Pastes:

```python
def add_paste(
    api_dev_key: str,
    paste_data: str, 
    custom_url: str = None, 
    user_name: str = None,
    paste_name: str = None,
    expire_date: str = None
    ) -> str:
    """
    Args:
        api_dev_key: The API developer key, this is also used to throttle
        paste_data: textual data of the paste
        custom_url: Optional custom URL
        user_name: Optional user name to be used to generate URL
        paste_name: Optional name of the paste
        expire_date: Optional expiration date for the paste
    
    Returns:
        A successful insertion returns the URL through which the paste can be
        accessed, otherwise it returns an error code
    """

def get_paste(api_dev_key: str, api_paste_key: str) -> str:
    """
    Where the api_paste_key represents the Paste Key of the paste to be retrieved
    This API will return the textual data of the paste
    """

def delete_paste(api_dev_key: str, api_paste_key: str) -> bool:
    """A successful deletion returns 'true' otherwise returns 'false"""
```

## Database Design

A few observations about the nature of the data we are storing:
1. We need to store billions of records
2. Each metadata object we are storing would be small (less than 1KB)
3. Each paste object we are storing can be of medium size (a few MBs)
4. There are no relationships between records, except if we want to store which user created what paste
5. Our service is read-heavy

#### Database Schema

We would need two tables, one for storing information about the Pastes, and the other for user's data

![](../../Attachments/Pasted%20image%2020230508221642.png)

Here, `URLHash` is the URL equivalent of the [Design TinyURL](Design%20TinyURL.md), and `ContentKey` is a reference to an external object storing the contents of the paste. We'll discuss the external storage of the paste contents later in this article.


## High Level Design

At a high level, we need an application layer that will serve all the read and write requests. The application layer will talk to a storage layer to store and retrieve data. We can segregate our storage layer with one database storing metadata related to each paste, users, etc. The other will store paste contents in some [Object Storage](../../Databases/Object%20Storage.md). This division of data will also us to scale each of them separately.

![](../../Attachments/Pasted%20image%2020230508221750.png)

## Component Design

#### Application Layer

Our application layer will process all incoming and outgoing requests. The application servers will be talking to the backend data store components to serve the requests.

**How to handle a write request?** Upon receiving a write request, our application server will generate a six-letter random string, which would serve as the key of the paste (if the user has not provided a custom key). The application server will then store the contents of the paste and the generated key in the database. After the successful insertion, the server can return the key to the user. One possible problem here could be that the insertion fails because of a duplicate key. Since we are generating a random key, there is a possibility that the newly generated key could match an existing one. In that case, we should regenerate a new key and try again. We should keep retrying until we don't see failure due to the duplicate key. We should return an error to the user if the custom key they have provided is already present in our database.

Another solution for the above problem could be to run a standalone **Key Generation Service** (KGS) that generates random six letter strings beforehand and stores them in a database (let's call it key-DB). Whenever we want to store a new paste, we will just take one of the already generated keys and use it. This approach will make things quite simple and fast since we will not be worrying about duplications and collisions. KGS will make sure all the keys inserted in key-DB are unique. KGS can use two tables to store keys, one for keys that are not used yet and one for all the used keys. As soon as KGS gives some keys to an application server, it can move these to the used keys table. KGS can always keep some keys in memory so that whenever a server needs them, it can quickly provide them. As soon as KGS loads some keys in memory, it can move them to the used keys table, this way we can make sure each server gets unique keys. If KGS dies before using all the keys loaded in memory, we will be wasting those keys. We can ignore these keys given that we have a huge number of them.

**Isn't KGS a single point of failure?** Yes it is, to solve this, we can have a standby replica of KGS and whenever the primary server dies it can take over to generate and provide keys.

**Can each app server cache some keys from key-DB?** Yes, this can surely speed things up. Although in this case, if the application server dies before consuming all the keys, we will end up losing those keys. This could be acceptable since we have 68 billion unique six letter keys, which are a lot more than we require.

#### Datastore Layer

We can divide our datastore layer into two:

1. Metadata database: We can use a relational database like MySQL or a Distributed Key-Value sotre like Dynamo or Cassandra.
2. Object Storage: We can store our contents in an object storage like [AWS S3](../../Software%20Engineering/AWS/AWS%20Data%20&%20Storage.md). Whenever we feel like hitting our full capacity on content storage, we can easily increase it by adding more servers.

![](../../Attachments/Pasted%20image%2020230508221801.png)

## Remaining Sections

For remaining sections like "Purging or DB Cleanup", "Data Partitioning and Replication", "Cache and Load Balancer", and "Security and Permissions" refer to (((LINK TINYURL)))