
Instagram is a social networking service that enables users to upload and share their photos and videos with other users. Instagram users can choose to share information either publicly or privately. Anything shared publicly can be seen by any other user, whereas privately shared content can only be accessed by the specified set of people. Instagram also enables its users to share through many other social networking platforms, such as Facebook, Twitter, Flickr, and Tumblr.

We plan to design a simpler version of Instagram for this design problem, where a user can share photos and follow other users. The 'News Feed' for each user will consist of top photos of all the people the user follows.

## Requirements

#### Functional Requirements
1. Users should be able to upload/download/view photos.
2. Users can perform searches based on photo/video titles.
3. Users can follow other users.
4. The system should generate and display a user's News Feed consisting of top photos from all the people the user follows.

#### Non-Functional Requirements
1. Our service needs to be highly available.
2. The acceptable latency of the system is 200ms for News Feed generation
3. Consistency can take a hit (in the interest of availability) if a user doesn't see a photo for a while, it should be fine.
4. The system should be highly reliable, any uploaded photo or video should never be lost.

Not in scope: Adding tags to photos, searching photos on tags, commenting on photos, tagging users to photos, who to follow, etc.

## Some Design Considerations

The system would be read-heavy, so we will focus on building a system that can retrieve photos quickly. 
1. Practically, users can upload as many photos as they like, therefore efficient management of storage should be a crucial factor in designing this system.
2. Low latency is expected while viewing photos.
3. Data should be 100% reliable. If a user uploads a photo, the system will guarantee that it will never be lost.

## Capacity Estimation and Constraints

- Let's assume 500M total users, with 1M daily active users (DAU).
- 2M new photos every day, 23 new photos every second
- Average photo file size = 200KB
- Total space required for 1 day of photos:
$$2M*200KB=400GB$$
- Total space required for 10 years:
$$400GB*365days*10years=1425TB$$

## High Level System Design

At a high-level, we need to support two scenarios, one to upload photos and the other to view/search photos. Our service would need some [Object Storage](../../Databases/Object%20Storage.md) servers to store photos and some database servers to store metadata information about the photos.

![](../../Attachments/Pasted%20image%2020230508221856.png)

## Database Schema

We need to store data about users, their uploaded photos, and the people they follow. The Photo table will store all data related to a photo, we will need to have an index on (`PhotoID`, `CreationDate`) since we need to fetch recent photos first.

![](../../Attachments/Pasted%20image%2020230508221901.png)

A straightforward approach for storing the above schema would be the use an [Relational Databases](../../Databases/Relational%20Databases.md) like MySQL since we require joins. But relational databases come with their challenges, especially when we need to scale them. We can store photos in an [Object Storage](../../Databases/Object%20Storage.md) like [AWS S3](../../Software%20Engineering/AWS/AWS%20Data%20&%20Storage.md).

We can store the above schema in a distributed key-value store to enjoy the benefits offered by [NoSQL](../../Databases/NoSQL.md). All the metadata related to photos can go to a table where the 'key' would be `PhotoID` and the 'value' would be the object containing `PhotoLocation`, `UserLocation`, `CreationTimestamp`, etc.

NoSQL stores, in general, always maintain a certain number of replicas to offer reliability. Also, in such data stores, deletes don't get applied instantly, data is retained for certain days (to support undeleting) before getting removed from the system permanently.

## Data Size Estimation

Let's estimate how much data will be going into each table and how much total storage we will need for 10 years.

#### User
Assuming each `int` and `dateTime` is 4 bytes, each row in the User's table will be 68 bytes:
`UserID` (4b) + `Name`(20b) + `Email`(32b) + `DateOfBirth`(4b) + `CreationDate`(4b) + `LastLogin`(4b) = 68 bytes

If we have 500 million users, we will need 32GB of total storage.
$$500M*68=32GB$$

#### Photo
Each row in the Photo's table will be of 284 bytes, `PhotoPath` will be 256 bytes and the rest of the fields will be 4 bytes. If 2M new photos get uploaded every day, we will need 500MB of storage for one day:
$$2M*284bytes=500MB$$
For 10 years, we will need 1.88TB of storage.

#### User Follow
Each row in the `UserFollow` table will consist of 8 bytes. If we have 500 million users and on average each user follows 500 users, we will need 1.82TB of storage:
$$500M*500followers*8b = 1.82TB$$
Total space required for all three tables for 10 years will be 3.7TB:
$$32GB + 1.88TB + 1.82TB = 3.7TB$$

## Component Design

Photo uploads (or writes) can be slow as they have to go to the disk, whereas reads will be faster, especially if they are being served from the cache.

Uploading users can consume all the available connections, as uploading is a slow process. This means that 'reads' cannot be served if the system gets busy with all the 'write' requests. We should keep in mind that web servers have a connection limit before designing our system. If we assume that a web server can have a maximum of 500 connections at any time, then it can't have more than 500 concurrent uploads or reads. To handle this bottleneck, we can split reads and writes into separate services. We will have dedicated servers for reads and different servers for writes to ensure that uploads don't hog the system.

Separating photo reads and writes will also allow us to scale and optimize each of these operations independently.

![](../../Attachments/Pasted%20image%2020230508222011.png)

## Reliability and Redundancy

Losing files is not an option for our service. Therefore, we will store multiple copies of each file so that if one storage server dies, we can retrieve the photo from the other copy present on a different storage server.

The same principle also applies to other components of the system. If we want to have high availability of the system, we need to have multiple replicas of services running in the system so that even if a few services die down, the system remains available and running. Redundancy removes the single point of failure in the system.

If only one instance of a service is required to run at any point, we can run a redundant secondary copy of the service that is not serving any traffic, but it can take control after the failover when the primary has a problem.

Creating redundancy in a system can remove single points of failure and provide a backup or spare functionality if needed in a crisis. For example, if there are two instances of the same service running in production and one fails or degrades, the system can failover to the healthy copy. Failover can happen automatically or require manual intervention.

![](../../Attachments/Pasted%20image%2020230508222017.png)

## Data Partitioning

Let's discuss different schemes for metadata sharding:

#### Partitioning on `UserID`
Let's assume we shard based on the `UserID` so that we can keep all photos of a user on the same shard. If one DB shard is 1TB, we will need four shards to store 3.7TB of data. Let's assume, for better performance and scalability, we keep 10 shards. So we'll find the shard number by `UserID % 10` and then store the data there. To uniquely identify any photo in our system, we can append the shard number with each `PhotoID`.

How can we generate `PhotoIDs`? Each DB shard can have its own auto-increment sequence for `PhotoIDs`, and since we will append `ShardID` with each `PhotoID`, it will make it unique throughout our system.

Now what are the issues with this partitioning scheme?
1. How would we handle hot users? Several people follow such hot users, and a lot of other people see any photo they upload.
2. Some users will have a lot of photos compared to others, thus making a non-uniform distribution of storage.
3. What if we cannot store all pictures of a user on one shard? If we distribute photos of a user onto multiple shards, will it cause higher latencies?
4. Storing all photos of a user on one shard can cause issues like unavailability of all of the user's data if that shard is down or higher latency if it is serving high load etc.

#### Partitioning on `PhotoID`
If we can generate unique `PhotoIDs` first and then find a shard number through `PhotoID % 10`, the above problems will be solved. We would not need to append `ShardID` with `PhotoID` in this case, as `PhotoID` will itself be unique throughout the system.

**How can we generate `PhotoIDs`?** Here, we cannot have an auto-incrementing sequence in each shard to define `PhotoID` because we need to know `PhotoID` first to find the shard where it will be stored. One solution could be that we dedicate a separate database instance to generate auto-incrementing IDs. If our `PhotoID` can fit into 64 bits, we can define a table containing only a 64 bit ID field. So whenever we would like to add a photo in our system, we can insert a new row in this table and take that ID to be our `PhotoID` of the new photo.

**Wouldn't this key generating DB be a single point of failure?** Yes, it would be. A workaround for that could be to define two such databases, one generating even numbered IDs and the other odd-numbered. For MySQL, the following script can define such sequences:
```
KeyGeneringServer1:
auto-increment-increment = 2
auto-increment-offset = 1

KeyGeneratingServer2:
auto-increment-increment = 2
auto-increment-offset = 2
```

We can put a [Load Balancer](../Load%20Balancer.md) in front of both of these databases to round-robin between them and deal with downtime. Both these servers could be out of sync, with one generating more keys than the other, but his will not cause any issue in our system. We can extend this design by defining separate ID tables for Users, Photo-Comments, or other objects present in our system.

Alternatively, we can implement a 'key' generation scheme similar to what is used in [Design TinyURL](Design%20TinyURL.md).

**How can we plan for the future growth of our system?** We can have a large number of logical partitions to accommodate future data growth, such that in the beginning, multiple logical partitions reside on a single physical database server. Since each database server can have multiple database instances running on it, we can have separate databases for each logical partition on any server. So whenever we feel that a particular database server has a lot of data, we can migrate some logical partitions from it to another server. We can maintain a config file (or a separate database) that can map our logical partitions to database servers, this will enable us to move partitions around easily. Whenever we ant to move a partition, we only have to update the config file to announce the change.

## Ranking and News Feed Generation

To create the News Feed for any given user, we need to fetch the latest most popular, and relevant photos of the people the user follows. For simplicity, let's assume we need to fetch the top 100 photos for a user's News Feed. Our application server will first get a list of people the user follows and then fetch metadata info of each user's latest 100 photos. In the final step, the server will submit all these photos to our ranking algorithm, which will determine the top 100 photos (based on recency, likeness, etc.) and return them to the user. A possible problem with this approach would be higher latency as we have to query multiple tables and perform sorting/merging/ranking on the results. To improve efficiency, we can pre-generate the News Feed and store it in a separate table.

#### Pre-Generating the News Feed
We can have dedicated servers that are continuously generating users' News Feeds and storing them in a `UserNewsFeed` table. So whenever any user needs the latest photos for their News-Feed, we will simply query this table and return the results to the user.

Whenever these servers need to generate the News Feed of a user, they will first query the `UserNewsFeed` table to find the last time the News Feed was generated for that user. Then, new News-Feed data will be generated from that time onwards (following the steps mentioned above).

#### Different Approaches to sending News Feed Content to Users

1. **Pull**: Clients can pull the News-Feed content from the server at a regular interval or manually whenever they need it. Possible problems with this approach are A: new data might not be shown to the users until clients issue a pull request, and B: most of the time, pull requests will result in an empty response if there is no new data.
2. **Push**: Servers can push new data to the users as soon as it is available. To efficiently manage this, users have to maintain a Long Poll request with the server for receiving the updates. A possible problem with this approach is a user who follows a lot of people or a celebrity user who has millions of followers. In this case, the server has to push updates quite frequently.
3. **Hybrid**: We can adopt a hybrid approach. We can move all the users who have a high number of followers to pull-based model and only push data to those who have a few hundred (or thousand) follows. Another approach could be that the server pushes updates to all the users not more than a certain frequency and letting users with a lot of updates to pull data regularly.

For more detailed discussion  about News Feed generation, refer to [Design Facebook's Newsfeed](Design%20Facebook's%20Newsfeed.md).

## News Feed Creation with Partitioned Data

One of the most important requirements to create the News Feed for any given user is to fetch the latest photos from all people the user follows. For this, we need to have a mechanism to sort photos on their time of creation. To efficiently do this, we can make photo creation time part of the `PhotoID`. As we will have a primary index on `PhotoID`, it will be quite quick to find the latest `PhotoIDS`.

We can use epoch time for this. Let's say our `PhotoID` will have two parts: the first part will be representing epoch time, and the second part will be an auto-incrementing sequence. So to make a new `PhotoID`, we can take the current epoch time and append an auto-incrementing ID from our key-generating DB. We can figure out the partition number from this `PhotoID` (`PhotoID % 10`) and store the photo there.

**What could be the size of our `PhotoID`?** Let's say our epoch time start today, how many bits would we need to store the number of seconds for the next 50 years?
$$3600seconds*24hours*365days*50years = 1.6Bseconds$$
We would need 31 bits to store this number. Since, on average, we are expecting 23 new photos per second, we can allocate 9 additional bits to store the auto-incremented sequence. So every second, we can store $2^9=512$ new photos. We are allocating 9 bits for the sequence number which is more than what we require. We are doing this to get a full byte (40 bits = 5 bytes). We can reset our auto-incrementing sequence every second.

This technique is further discussed in [Design Twitter](Design%20Twitter.md).

## Cache and Load Balancing

Our service would need a massive-scale photo delivery system to serve globally distributed users. Our service should push its content closer to the user using a large number of geographically distributed photo cache servers and user [CDN](../CDN.md).

We can introduce a [Database Cache](../../Databases/Database%20Cache.md) for metadata servers to cache hot database rows. We can use Redis to cache the data, and Application servers before hitting the database can quickly check if the cache has desired rows. Least Recently Used (LRU) can be a reasonable cache eviction policy for our system.

**How can we build a more intelligent cache?** If we go with the 80-20 rule, i.e. 20% of daily read volume for photos is generating 80% of the traffic, which means that certain photos are so popular that most people read them. This dictates that we can try caching 20% of the daily read volume of photos and metadata