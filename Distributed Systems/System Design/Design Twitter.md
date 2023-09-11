
*Twitter* is a social networking service where users post and read short 140-character messages called "tweets". Registered users can post and read tweets, but those who are not registered can only read them. User's access Twitter through their website interface, SMS, or mobile app.

## Requirements

We will be designing a simpler version of Twitter with the following requirements:

#### Functional Requirements
1. Users should be able to post new tweets.
2. A user should be able to follow other users.
3. Users should be able to mark tweets as favorites
4. The service should be able to create and display a user's timeline consisting of top tweets from all the people the user follows.
5. Tweets can contain photos and videos

#### Non-Functional Requirements
1. Our service needs to be highly available
2. Acceptable latency of the system is 200ms for timeline generation
3. Consistency can take a hit (in the interest of availability). If a user doesn't see a tweet for a while, it should be fine.

#### Extended Requirements
1. Searching for tweets.
2. Replying to a tweet.
3. Trending topics - current hot topics/searches.
4. Tagging other users.
5. Tweet Notification
6. Who to follow? Suggestions?
7. Moments.

## Capacity Estimation and Constraints

Let's assume we have one billion total users with 200 million daily active users (DAU). Also assume we have 100 million new tweets every day and on average each user follows 200 people.

**How many favorites per day?** If on average, each user favorites five tweets per day we will have 1 billion favorites a day.

**How many total tweet-views will our system generate?** Let's assume on average a user visits their timeline two times a day and visits five other people's pages. On each page if a user sees 20 tweets, then our system will generate 28 billion total tweet views a day.

#### Storage Estimates

Let's say each tweet has 140 characters and we need two bytes to store a character without compression. Let's assume we need 30 bytes to store metadata with each tweet (like ID, timestamp, user ID, etc.). Thats 310 bytes times the 100 million daily tweets, which totals 30 GB per day of storage.

How much storage would we need for five years?  How much storage would we need for user's data, follows, and favorites? Not all tweets have media, let's assume that 20% of tweets have a photo and 10% have a video. Let's also assume each photo is 200KB and a video is 2MB. This will lead us to using 24TB of total data every day:
$$(0.2)100M*200KB + (0.1)100M*2MB + 30GB = 24TB$$

#### Bandwidth Estimates

Since total ingress is 24TB per day, this would translate to 290MB/sec

Remember we have 28 billion tweet views per day. We have to show every photo, but let's assume users only view 1/3 videos. So total egress would be:
$$\frac{(28B*310bytes)}{86400sec} + \frac{((0.2)28B*200KB)}{86400sec} + \frac{((0.1)(0.33)28B*2MB)}{86400sec} = 35GB/sec$$

## System APIs

The following could be a definition of the API for posting a new tweet:
```python
def tweet(
    api_dev_key: str, 
    tweet_data: str, 
    tweet_location: str, 
    user_location: str, 
    media_ids: list[int]
) -> str:
    """
    Args
        api_dev_key: the API developer key of the registered account. This will be used to, among other things, throttle users based on their allocated quota.
        tweet_data: The text of the tweet
        tweet_location: Optional location (long, lat) this tweet refers to
        user_locaiton: Optional location of the user
        media_ids: Optional list of media_ids to be associated with the Tweet. (all the media photo, video, etc. needs to be uploaded separately)

    Returns
        A successful post will return the URL to access that tweet. Otherwise, an appropriate HTTP error is returned
        """
```

## High Level System Design

We need a system that can efficiently store all the new tweets. There are 1150 writes and 325K reads per second, so this is a read-heavy system.

At a high level, we need multiple application servers to serve all these requests with load balancers in front of them to distribute load. On the backend, we need an efficient database that can store all the new tweets and can support a huge number of reads. We also need object storage to store photos and videos.

![](../../Attachments/Pasted%20image%2020230709173841.png)

The traffic will have peaks and troughs throughout the day. At peak time, we should be able to handle at leave a few thousand and around 1 million reads per second. We should keep this in mind while designing the architecture.

## Database Schema

We need to store data about users, their tweets, their favorite tweets, and people they follow:

![](../../Attachments/Pasted%20image%2020230709173847.png)

We can go with a Relational Database like Postgres, but that comes with scaling challenges with sharding. Alternatively we can go with a distributed key-value store to allow easy scaling. All tweets, user data, photo metadata, etc. would be in the database. The photos and videos would be in an object store like S3.

## Data Sharding

There are two options by which we can shard the nodes

#### Sharding based on `UserID`

We can try storing all the data of a user on one server. While storing, we can pass the UserID to our hash function that will map the user to a database server where we will store all of the user's tweet, favorites, follows, etc. While querying for tweets/follows/favorites of a user, we can ask our hash function where to find data. This approach has a few drawbacks though:
1. What if there is a hot user? There could be a huge load on a single server containing Kim Kardashian.
2. Over time some users can end up storing a lot of tweets or having a lot of follows compared to others.

To try to fix these issues, we either have to repartition or redistribute our data or use consistent hashing.

#### Sharding based on `TweetID`

Our hash function will map each TweetID to a random server where we will store that Tweet. To search for tweets, we have to query all servers, and each server will return a set of tweets. A centralized server will aggregate these results to return them to the user. Let's look into a timeline generation example. Here are the steps our system would have to do to generate a user's timeline:
1. Our application server will find all the people the user follows
2. The app server will send the query to all database servers to find tweets from these people.
3. Each database server will find the tweets for each user, sort them by date, and return the top tweets
4. The app server will merge all the results and sort them again by timestamp to return the top results to the user.

This method solves the problem of hot users, but we then have the performance hit of having to query all partitions to find the tweets of a user. We can further improve our performance by introducting a cache to store hot tweets in front of the database servers.

#### Sharding based on Timestamp

Storing tweets based on creation time will give us the advantage of fetching all the top tweets quickly and we only have to query a very small set of servers. The problem here is that the traffic load will be very skewed to the machines holding the most recent tweets.

#### Sharding on both `TweetID` and Timestamp

If we don't store tweet create time separately and use `TweetID` to reflect that, we can get the benefits of both approaches. This way, it will be quick to find the latest tweets. For this, we must make each `TweetID` universally unique and each `TweetID` should contain a timestamp too. We can use epoch time for this. Let's say our `TweetID` will have two parts: the first part will represent the elapsed time in seconds, and the second part will be an auto-incrementing number. We can figure out the shard number from this TweetID and store it there.

![](../../Attachments/Pasted%20image%2020230709173903.png)

## Timeline Generation

Refer to [Design Facebook's Newsfeed](Design%20Facebook's%20Newsfeed.md).


## Replication and Fault Tolerance

Since our system is read-heavy, we can have multiple secondary database servers for each DB partition. Secondary servers will be used for read traffic only. All writes will first go to the primary server and then will be replicated to secondary servers. This scheme will also give us fault tolerance, since whenever the primary server goes down we can failover to the secondary.

## Load Balancing

We can add a load balancer at three locations in our system:
1. Between the client and the application servers
2. Between application servers and data replication servers
3. Between aggregation servers and cache servers.

Initially, a simple round robin approach can be used, this doesn't require any overhead. We can also use a more intelligent LB algorithm that periodically polls servers to see their load and adjust traffic based on that.

## Monitoring

Having the ability to monitor our systems is crucial. We should constantly collect data to get an instant insight into how our system is doing. We can collect the following telemetry:
1. How many new tweets per day/second, and what is the daily peak?
2. Timeline delivery stats, how many tweets per day/second is our service delivering
3. Average latency that is seen by the user when generating a timeline.

## Extended Requirements

**How do we serve feeds?** Get all the latest tweets from the people someone follows and merge/sort them by time. Use pagination to fetch/show tweets. Only fetch top N tweets from all the people someone follows. This N will depend on the client's viewport, e.g. we'll load fewer tweets on mobile. We can also cache the next N tweets to speed things up.

Alternatively we can pre-generate the feed to improve efficiency. For details look at Designing Instagram.

**Retweet:** With each Tweet object in the database, we can store the ID of the original Tweet and not store any contents on this retweet object.

**Trending Topics:** We can cache the most frequently occurring hashtags or search queries in the last N seconds and keep updating these after M seconds. We can rank trending topics based on the frequency of tweets or search queries or any other metric.

**Who to follow? How to give suggestions?** This feature will improve user engagement. We can suggest friends of people someone follows. We can go two or three levels down to find famous people for the suggestions. We can give preference to people with more followers.

As only a few suggestions can be made at any time, we can use an ML Recommender System to shuffle and re-prioritize recommendations.

**Moments:** Get the top news for different websites in the past 1 or 2 hours. figure out related tweets, prioritize them, categorize them (news, support, financial, entertainment, etc.) also using ML.

**Search:** Search involves indexing, ranking, and retrieval of tweets. A similar solution is discussed in Designing Twitter Search


## Cache

We can cache hot tweet and users to help latency and traffic. We can use a solution like Memcached that can store entire Tweet objects. App servers can check the cache before hitting the database. Based on client's usage patterns, we can determine how many cache servers we need.

**Which cache replacement policy would best fit our needs?** When the cache is full, Least Recently Used (LRU) is a reasonable cache eviction policy to use. Under this policy, we discard the least recently viewed tweet.

**How can we have a more intelligent cache?** We can go with the 80/20 rule, that is 20% of tweets generating 80% of traffic. This dictates that we should try to cache 20% of daily read volume to each partition.

**What if we cache the latest data?** Our service can benefit from this approach. Let's say if 80% of users see tweets from the past three days only, we can try to cache all the tweets from the past three days. As estimated above, we are getting 100 million new tweets a day or 30GB of new data (without photos and videos). If we want to store all the tweets from the last three days, we will need less than 100Gb of memory. This data can easily fit into one server, we should replicate it to distribute the read traffic.

Our cache would be like a hash table where `key` would be `OwnerID` and `value` would be a doubly linked list containing all the tweets from that user in the past three days. Since we want to retrieve the most recent data first, we can always insert new tweets at the head of the linked list, which means all the older tweets will be near the tail of the linked list. Therefore, we can remove tweets from the tail to make space for newer tweets.

![](../../Attachments/Pasted%20image%2020230709173914.png)