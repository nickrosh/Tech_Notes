

Once you start expanding a [Relational DB](Relational%20DB.md) and need to handle more read and write requests, your first action should be creating replicas of the data and [Load Balancing](Load%20Balancer.md) among the various nodes to keep the load manageable.


## Leader-Follower Replication

![](Pasted%20image%2020220417171545.png)

The most common architecture for replication is the **Leader-Follower** (also known as Master-Slave) model where there is one _Leader_ node where all the writes are sent to, and many replica _Follower_ Nodes that handle all the read requests.

### Outages
If a follower has an outage, you merely need to create a new follower and copy the latest snapshot from the leader

If the leader has an outage, then a failover procedure is followed. First, the outage is verified by waiting for a timeout period. If the leader fails to respond, then the follower with the most up-to-date data is promoted to leader and all subsequent writes are sent to the new leader node. Of course, this may cause some data loss if there was a discrepancy between the new leader and the old one at the time of the outage.

### Replication Logs
The underlying implementation of replication relies of a _Replication Log_ that stores the state of the leader database. This way, a source is truth is able to be verified and new followers can be quickly replicated. The major implementations are:

- **Statement-Based Replication**: A log of every `INSERT`, `UPDATE`, and `DELETE` command is stored and used as the basis to replicate data. This causes problems with non-deterministic data like using the current time or a random number.  
- **Write Ahead Log (WAL)**: An append only log of all the writes sent to the leader is used as the basis of replication, used by **PostgreSQL** and **Oracle**. The leader sends a copy across the network to its followers. When a follower processes the log, it builds a data structure identical to the leader. This methods uses low-level details of the DB engine and is thus not backwards compatible.
- **Logical (row based) Log Replication:** A sequence of records describing every write to the database on a row-level. This includes every Insert, update, and delete on a row by row basis. This is used by **MySQL** for its row replication. Since it does not depend on low level engine details, this method is more easily backwards-compatible and even cross-engine compatible.


### Replication Lag
As the number and distance of replicas grows, a lag occurs between when the leader is written to and when the followers all have the most up to date data. This is what is referred to as **Eventual Consistency**. This becomes an issue when a client sees data backwards in time, especially if it's data that they just updated. There are several ways to solve this:

- **Reading your own writes:** To ensure that a user always sees the data that they just updated, you can institute logic that sends all read requests of a users own data to the leader node, which is always up to date. This however does not scale very well as it puts additional strain on the singular leader
- **Monotonic Reads:** If you read from different followers at different stages of replication, you may be data this _back in time_ like comments out of order. One solution is to make sure a user always reads from the same follower, that way they will only see data in a linear time.
- **Consistent Prefix Reads:** A problem particularly with [DB Partitions](DB%20Partitions.md) is when you read the wrong order of writes coming from different partitions. One solution to this is to make sure that any writes that are causally related to each other are written to the same partition.


## Multi-Leader Replication
![](Pasted%20image%2020220417174437.png)

In some cases, we may want to have multiple leaders. This helps scale write throughput and allows us to have leaders in multiple datacenters to speed up the entire write-replication process in multiple regions. There are serious issues and complexity with making sure the data is consistent across all leaders and followers however.

### Handling Write Conflicts
![](Pasted%20image%2020220417174434.png)

The best solution to write conflicts is simply **Conflict Avoidance**, trying your best to avoid scenarios where multiple leaders are writing to the same data. If you can't avoid conflict, then you must deal with conflicts and **Converge towards a consistent state**. Here is a popular way of doing this:

>Give each write a unique ID, pick the write with the highest ID as the _Winner_ and throw away the other writes. If done with a timestamp, this is known as **Last Write Wins (LWW)**, which is very popular but very prone to data loss.

You can also write your own custom conflict resolution logic, which may be better suited to your individual problem.


## Leaderless Replication
Made popular by Amazon's **DynamoDB**, in this method, every node accepts both reads and writes. This allows for potentially limitless scaling, but provides its own challenges with determining the true state of the data at any given time.

### Read Repair & Anti-Entropy Process
In this scheme, eventually all data should be copied to every node to ensure eventual consistency. Read Repair is when a user makes a request for some data, some of the nodes may contain stale data at which time the fresh nodes will update the stale data. This however, requires the client to make a request for the data, there is also **Anti-Entropy Process** which is a background process that scans for stale data and updates them. Node outages do not cause and issue as every node has the same read and write capabilities.


### Quorum Consistency
![](Pasted%20image%2020220417181137.png)

Say you make a read or a write and only 2 out of 3 nodes respond that it was successful. Can that be considered a successful operation? In fact, we can determine whether an operation was successful if it reaches a quorum with all the nodes. If there are **N** replicas, every write must be confirmed by **W** nodes to be considered successful, and we must read **R** nodes for each read. As long as $W + R > N$, we expect to get an up-to-date value when reading.

There are some edge cases, however where you can still get stale results even if W+R>N.

- If a **Sloppy Quorum** is used, the W writes can end up on different nodes than the R reads, so there is no longer a guaranteed overlap between the R nodes and the W nodes.
- If two writes occur concurrently, and it is not clear which happened first, then we have to merge the writes. This could be throwing away the last written one, although that could be wrong due to clock skew. We could also merge the writes like inserting a union of two concurrent inserts, but this could be wrong as well.