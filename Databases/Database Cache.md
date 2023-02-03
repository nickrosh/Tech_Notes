
A *database cache* is an [in-memory](../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) data store that is commonly used to cache user requests to a web server. By storing data in RAM, client requests can be responded to much faster than going to the database, which is stored on disk. like hard drives and [Flash Memory](../Electrical%20Engineering/Digital/Flash%20Memory.md). Cache's are almost always key-value stores, like a [Hash Maps](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md). The most popular cache software is *Memcached* and *Redis* (Redis also doubles as a [NoSQL](NoSQL.md) key-value consistent database). However, the cache is usually smaller than the entire database, so the requested objects will not always be on a cache. When the cache contains the data the client is requesting, that is referred to as a *cache hit*. If the cache does not contain the data, that is a *cache miss*. The ratio of the total number of hits divided by total misses is referred to as the *cache ratio*.


## Caching Strategies

There are several different strategies you can employ when reading and writing to a cache and the database. This strategy will have effects on database [Consistency](../Distributed%20Systems/Consistency%20and%20Consensus.md) and cache availability.

#### Cache-Aside

In a *Cache-Aside* arrangement, the database cache sits next to the database. When the application requests the data, it will check the cache first. If the cache has the data (cache hit), then it will return it. If the cache does not have the data (cache miss), then the application will query the database. The application then stores that data in the cache for any subsequent queries.

![](../Attachments/Pasted%20image%2020230202212754.png)

This design is a good general purpose caching strategy. This is particularly useful for read-heavy workloads. This keeps frequently read data close at hand for the many incoming read requests. Two additional benefits stem from the cache being separated from the database, this provides some resiliency. Secondly, with the cache being separated, it can employ a different data model than the database. The main drawback is the window being open for inconsistency from the database. Generally, any data being written will go to the database directly. Therefore the cache may have a period of inconsistency with the database.

#### Read-Through

In a *read-through* cache arrangement, the cache sits between the application and database. It can be envisioned like a straight line from the application to database with the cache in the middle. In this strategy, the application will always speak with the cache for a read, and when there is a cache hit, the data is immediately returned. In the case of a cache miss, the cache will populate the missing data from the database and then return it to the application. For any data writes, the application will still go directly to the database.

![](../Attachments/Pasted%20image%2020230202213329.png)

Read-through is also good for read-heavy workloads. The main differences between read-through and cache-aside is that in a cache-aside strategy the application is responsible for fetching the data and populating the cache, while in a read-through setup, the logic is done by a library or some separate cache provider. A read-through strategy also has the disadvantage of needing to go to the database to get the data anytime a new read request comes through . This data has never been cached before so therefore the data needs to be loade3d. It is common for developers to mitigate this delay by "warming" the cache by issuing likely to happen queries manually.

#### Write-Through

A *write-through* strategy differs from the previous two because of instead of writing data to the database, it will write to the cache first and the cache immediately writes to the database. This arrangement is read-through except in the other direction.

![](../Attachments/Pasted%20image%2020230202213924.png)

The benefit of a write-through strategy is that the cache is ensured to have any written data and no new read will experience delay while the cache requests it from the main database. If solely making this arrangement, there is the big disadvantage of extra write latency because the action must go to the cache and the n to the database. This should happen immediately, but there is still two writes occurring in succession. 

The real benefit comes from pairing write-through with a read-through cache. This strategy will adopt all the aforementioned benefits of the read-through caching strategy with the added benefit of removing eh potential for data inconsistency.

#### Write-Back

*Write-back* works almost the exact same as write-through except for one key detail. In a write-back strategy, the application again writes directly to the cache. However, the cache does not immediately write to the database, and it instead writes after a delay.

![](../Attachments/Pasted%20image%2020230202214348.png)

By writing to the database with a delay instead of immediately, the strain on the cache is reduced in a write-heavy workload. This makes a write-back, read-through combination good for mixed-workloads. This pairing ensures that the most recently written data and accessed data is always present and accessible via the cache.

The delay in cache to database writes can improve overall write performance and if [batching](Transactions.md) is supported, then also a reduction in total writes. This opens up the potential for some cost savings and overall workload reduction. However, in the case of a cache failure, this delay opens the door for possible data loss (a cache is not [durable](ACID.md)) if the batch or delayed write to the database has not yet occurred.

#### Write-Around

A *write-around* strategy will be combined with either a cache-aside or a read-through. In this arrangement, data is always written to the database and the data that is read goes to the cache. If there is a cache miss, then the application will read from the database and then update the cache for next time.

![](../Attachments/Pasted%20image%2020230202214834.png)

This strategy is going to be most performant in instances where data is only written once and not updated. The data is read very infrequently or not at all.


## Eviction Policy

When the cache reaches its limit of memory storage, we have to evict some data to allow new data in. The policy will be chosen to maximize the cache hit ratio. The simplest strategy would be to just use a [Queue](../Data%20Structures%20&%20Algorithms/Data%20Structures/Queues.md) in a first-in first-out algorithm. Thus we use more advanced algorithms

#### Least Recently Used (LRU)

*Least Recently Used* as the name implies will evict the data that has been the least recently used. This is like using a Queue, except whenever an object is read, it is removed from the queue and added back to the front i.e. most recently used. LRU is usually the best choice for a cache.

#### Least Frequently Used (LFU)

*Least Frequently Used* is a similar algorithm that keeps a [Hash Map](../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md) counter for all the data in the cache. The number of times each object is read is recorded by the counter, so when something needs to be evicted, we will evict the data with the fewest reads. A drawback with LFU is that highly read data from a previous day might overstay when it is needed in the cache.