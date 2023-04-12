
The System Design Interview is a high level open-ended design problem where you are asked to design a large distributed system, usually emulating a real application, like Twitter or Google Drive. There is no one ultimate answer, you must create a complete design while demonstrating your competency.

## Step 1: Requirements

You must ask clarifying questions of the exact scope of the problem we are trying to solve. Design questions are inherently open ended, and they don't have one correct answer. That's why clarifying ambiguities early in the interview becomes critical.

You must clarify all requirements, and differentiate between functional and non-functional requirements. Functional requirements are the actual functioning of the application, like adding likes to a tweet, or news feed generation. This can be very open ended and must be clarified with the interviewer.

Non-functional requirements are everything other than the actual functions of the application including things like [scalability, throughput, latency, etc.](Design%20Requirements.md). Handling very large scale is usually the hardest part of the interview, and real life [distributed systems](../Distribution%20Problems.md). The choice of availability versus consistency as laid out in [CAP Theorem](../CAP%20Theorem.md) is also a non-functional requirement that needs to be spelled out. Also the specific availability percent target needs to be defined.

Here are some questions for designing a Twitter-like service that should be answered before moving on:
- Will users of our service be able to post tweets and follow other people?
- Should we also design to create and display the user's timeline?
- Will tweets contain photos and videos?
- Are we focusing on the backend only, or are we developing the front-end too?
- Will users be able to search tweets?
- Do we need to display how trending topics?
- Will there be any push notifications for new (or important) tweets?

These clarifying questions will determine how our end design will look like.

## Step 2: Back of the envelope calculations 

Let's say you assume you have 100 million Daily Active Users (DAU). Let's say it's a read heavy system so we can assume 10 billion reads and 10 million writes every day. You can calculate the read throughput the following way:
$$\text{Throughput} = \frac{10b}{\text{second}}$$
We calculate the number of seconds to be approximately 100,000:
$$\text{seconds per day} = 60*60*24 = 86,000s \approx 100,000s$$
Thus we can easily calculate the following:
$$\text{Throughput} = \frac{10b}{100k} = 100 \text{reads per second}$$

The following are some latency metrics of common computation and network tasks. From the fastest [Memory & Cache](../../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) reads, to slower [Flash Memory](../../Electrical%20Engineering/Digital/Flash%20Memory.md) to painfully slow [HTTP](../../Internet%20&%20Networking/HTTP.md) requests over the network. This image also provides extra motivation for [Database Caches](../../Databases/Database%20Cache.md) and [CDNs](../CDN.md).

![](../../Attachments/Pasted%20image%2020230405003749.png)

## Step 3: System Interface Definition

Define what [APIs](../../Internet%20&%20Networking/REST%20API.md) are expected from the system. This will establish the exact contract expected from the system and ensure if we haven't gotten any requirements wrong. Some examples of API's for our Twitter-like service would be:
```python
post_tweet(user_id, tweet_id, tweet_location, user_location, timestamp, ...)

generate_timeline(user_id, current_time, user_location, ...)

mark_tweet_favorite(user_id, tweet_id, timestamp, ...)
```


## Step 4: Defining the Data Model

Defining the data model in the early part of the interview will clarify how data will flow between different system components. Later, it will guide you for [DB Partitions](../../Databases/DB%20Partitions.md) and management. The candidate should identify various system objects, how they will interact with each other, and different aspects of data management like storage, transportation, encryption, etc. Here are some objects for our Twitter-like service:

**User:** User ID, Name, Email, birthday, creation date, last login, etc.
**Tweet:** tweet ID, content, tweet location, likes, timestamp, etc.
**User Follow:** User ID 1, User ID 2
**Favorite Tweets:** User ID, Tweet ID, timestamp

Which database system should we use? Will [NoSQL](../../Databases/NoSQL.md) like Cassandra best fit our needs or a [Relational Databases](../../Databases/Relational%20Databases.md) like MySQL? What king of [Object Storage](../../Databases/Object%20Storage.md) is needed to store the photos and videos.

## Step 5: High-Level Design

Draw a block diagram with 5-6 boxes representing the core components of our system. We should identify enough components that are needed to solve the actual problem from end to end.

For Twitter, at a high level, we will need multiple application servers to serve all read/write requests with [Load Balancer](../Load%20Balancer.md) in front of them for traffic distributions. If we're assuming that we will have a lot more read traffic (compared to writes), we can decide to have separate server to handle these scenarios. On the back-end, we need an efficient database that can store all the tweets and support a large number of reads. We will also need a distributed [Object Storage](../../Databases/Object%20Storage.md) for storing photos and videos.

![](../../Attachments/Pasted%20image%2020230405010141.png)


## Step 6: Detailed Design

Dig deeper into two or three major components: the interviewer's feedback should always guide you to what parts of the system need further discussion. We should present different approaches, their pros and cons, and explain why we will prefer one approach over the other. Remember, there is no single answer, the only important thing is to consider tradeoffs between different options while keeping system constraints in mind.
- Since we will be storing a massive amount of data, how should we [Partition](../../Databases/DB%20Partitions.md) our data to distribute it to multiple databases? Should we try to store all the data of a user on the same database? What issue could it cause?
- How will we handle hot users who tweet a lot or follow lots of people?
- Since users' timeline will contain the most recent (and relevant) tweets, should we try to store our data so that it is optimized for scanning the latest tweets?
- How much and at which layer should we introduce [Caching](../../Databases/Database%20Cache.md) to speed things up?
- What components need better [Load Balancing](../Load%20Balancer.md)?

## Step 7: Identifying and Resolving Bottlenecks

Try to discuss as many bottlenecks as possible and different approaches to mitigate them.
- Is there any single point of failure in our system? What are we doing to mitigate it?
- Do we have enough [replicas](../../Databases/DB%20Replication.md) of the data so that we can still serve our users if we lose a few servers?
- Similarly, do we have enough copies of different services running such that a few failures will not cause a total system shutdown?
- How are we monitoring the performance of our service? Do we get alerts whenever critical components fail or their performance degrades?