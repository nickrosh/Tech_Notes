
At a certain point, there is so much data held within a Database that it must be partitioned (or sharded). This is a very difficult and complex process, especially for [Relational DBs](Relational%20Databases.md) so it is advisable to avoid partitioning until absolutely needed, and first rely on [DB Replication](DB%20Replication.md). In fact, replication and partitioned are combined such that each partition has multiple replicas.

## Partitioning of Key-Value Data

The primary question is how to optimally assign data records to different nodes. If a partition is unfair, so that some partitions have more data or queries than other, we call it *skewed*. A partition with disproportionately high load is called a *hot spot*. The simplest solution would be random assignment

#### Partitioning by Key Range

One method to partitioning is to assign a continuous range of keys to each partition. Within each partition, we can keep keys in sorted order. This has the advantage that range scans are easy, and you can treat the key as a concatenated index in order to fetch several related records in one query. For instance, in a DB that is indexed by timestamp, it becomes very easy to query a particular time like a month of continuous data. However the downside of this is also that it creates hot spots. In the timestamp example, the most recent month may be accessed repeatedly while historical data is untouched.

#### Partitioning by Hash of Key

The solution to skews and hot spots is to take the a hash function of the key to determine its partition. This has the benefit of being uniformly distributed fairly among the partitions. The partition boundaries can be evenly spaced or pseudo randomly, also known as *consistent hashing*. Lastly, in unusual scenarios there can be skew and hotspots with Hashed partitions. Like in the case of a social media celebrity receiving enormous traffic. A simple technique is to add a random number to the beginning or end of the key. Just a two-digit decimal random number would split the writes to the key evenly across 100 different keys, allowing those keys to be distributed to different partitions. However, this split increases the workload for reads as they now have to read from all of the different keys.


## Partitioning and Secondary Indexes

Besides simple key-value modeling, the situation becomes more complicated when secondary indexes beyond the primary index are involved. Secondary indexes are the bread and butter of [Relational Databases](Relational%20Databases.md) and are common in document databases too. The problem is that they don't map cleanly to partitions.

#### Partitioning Secondary Indexes by Document

![](Pasted%20image%2020221130224756.png)

In this approach, each partition is completely separate: each partition maintains its own secondary indexes, covering only the documents in that partition. It doesn't care what data is stored in other partitions. Whenever you need to write to the database- to add, remove, or update a document- you only need to deal with the partition that contains the document ID that you are writing. For that reason, a document partitioned index is also known as a *local index*. This method is popular with [Document DBs](Document%20Databases.md).

This method makes no guarantee everything you are searching for will be in the same partition. You have to query every partition when you make a secondary index query and combine all the results you get back. This is sometimes referred to as *scatter and gather*, and it can make reads quite expensive. DB vendors thus recommend that you try to make your secondary scheme work so it will only search on one partition, but this is not usually possible.

#### Partitioning Secondary Indexes by Term

![](Pasted%20image%2020221130225553.png)

Rather than every partition having its own secondary index, we can construct a global index that covers data in all partitions. However, we can't just store that index on one node, since it would likely become a bottleneck and defeat the purpose of partitioning. A global index must also be partitioned, but it can be partitioned differently from the primary key index.

The advantage of global (or term-partitioned) indexes over a document-partitioned index is that it can make reads more efficient: rather than doing scatter/gather over all partitions, a client can make a request to the partition containing the term that it wants. However, the downside of a global index is that writes are  and more complicated, because a write to a single document may now affect multiple partitions of the index.

## Rebalancing Partitions

Over time, things change in a database, from increased load, to larger datasets, to machine failure. All of these changes call for data and requests to be moved from one node to another. The process of moving from one node in the cluster to another is called *rebalancing*. No matter what partitioning or rebalancing scheme is used, the following is held to a minimum:

1. After rebalancing, the load should be fairly shared between the nodes
2. While rebalancing is happening, the DB should continue accepting reads and writes
3. No more data than necessary should be moved between nodes

*Note*: There is a debate between Manual and Automatic rebalancing. While [Consistent Delivery](CI%20&%20CD.md) is all the rage, it's still always good to have a human in the loop.

#### Rebalancing a Fixed Number of Partitions

![](Pasted%20image%2020221130231959.png)

There is a simple solution: create many more partitions than there are nodes, and assign several partitions to each node. This way, entire partitions are moved between nodes, and we can ensure only partition assignment to nodes is what is changing. This goes back to the original point however, that the number of partitions must be carefully considered. Large partitions make rebalancing expensive, and small partitions have overhead.

#### Dynamic Partitions

Fixed partitions are very inconvenient for key-range partitions, as the ranges could be completely ruined by rebalancing. Hence why the other method is dynamically splitting and merging partitions as they grow too large or shrink below a minimum. Another advantage is that the number of partitions adapts to the size of the dataset. As the system creates new partitions, it will transfer them between nodes to ensure load balancing. This method is not only useful to key-range DBs, popular products like MongoDB default to Dynamic partitioning.

#### Partitioning Proportionally to Nodes

For both fixed and Dynamic partitioning, the partition scheme is done according the size of the data. A third option is to make the number of partitions proportional to the number of nodes. In this case, the size of each partition. When a new node joins the cluster, it randomly chooses a fixed number of existing partitions to split, and then takes ownership of one half of each of those split partition while leaving the other half of each partition in place. The randomization can produce unfair splits, but this effect is minimized over a larger number of partitions.

#### Manual or Automatic?

The one important question in regards to rebalancing partitions is: does the rebalancing happen automatically or manually? In the quest for ever more automation and [CI & CD](CI%20&%20CD.md) many push for fully automated rebalancing. However, it can be unpredictable. Rebalancing is an expensive operation, because it requires rerouting requests and moving a large amount of data from one node to another. If it is not done carefully, this can overload the network or the nodes and harm the performance of other requests while rebalancing is in progress. For these reasons, it is usually a good idea to have a human in the loop.


## Request Routing

We have now partitioned our dataset across multiple nodes running on multiple machines, but there remains an open question: when a client wants to make a request, how does it know which node to connect to? This is an instance of a more general problem called *service discovery*

![](Pasted%20image%2020221228210448.png)

There are a few different approaches we can do:
1. Allow clients to contact any node (e.g. via a round-robin [Load Balancer](Load%20Balancer.md)) If that node coincidentally own the partition to which the request applies, it can handle the request. Otherwise, it is routed to the correct node.
2. Send all requests from the clients to a routing tier first, which determines the node that should handle each request and forwards it accordingly. This routing tier acts only as a partition-aware [Load Balancer](Load%20Balancer.md).
3. Require that clients be aware of the partitioning and the assignment of partitions to nodes. In this case, a client can connect directly to the appropriate node.

This is a hard problem. Many systems rely on a separate coordination service such as Zookeeper to keep track of this cluster metadata.

![](Pasted%20image%2020221228210856.png)

Whenever a partition changes ownership or a node is added or removed, Zookeeper notifies the routing tier so that it can keep its routing information up to data.