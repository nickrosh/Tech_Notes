
Once you start expanding a [Relational Databases](Relational%20Databases.md) and need to handle more read and write requests, your first action should be creating replicas of the data and [Load Balancing](../Distributed%20Systems/Load%20Balancer.md) among the various nodes to keep the load manageable.


## Leader-Follower Replication

![](../Attachments/Pasted%20image%2020220417171545.png)

The most common architecture for replication is the **Leader-Follower** (also known as Master-Slave) model where there is one _Leader_ node where all the writes are sent to, and many replica _Follower_ Nodes that handle all the read requests.

#### Synchronous Versus Asynchronous Replication

![](../Attachments/Pasted%20image%2020221227184840.png)

See the example above. The replication to Follower 1 is *synchronous*: the leader waits until follower 1 has confirmed that it received the write before reporting success to the user, and before making the write visible to other clients. The replication to Follower 2 is *asynchronous*: the leader send the message, but doesn't wait for a response from the follower.

It is impractical for all followers to be synchronous, any node outage would cause the whole system to grind to a halt. In practice, if you enable *synchronous replication* on a database, it usually means *one* of the followers is synchronous, and the others are asynchronous. If the synchronous follower becomes unavailable or slow, one of the other followers takes its place. This guarantees that you have an up-to-date copy of the data on at least two nodes: the leader, and the synchronous follower.

#### Setting Up New Followers

From time to time, you need to set up new followers. Simply copying the data files from one node to another is typically not sufficient: clients are constantly writing to the DB, and the data is always in flux. The proper sequence would be as follows:
1. Take a consistent snapshot of the leader's database at some point in time-- if possible, without taking a lock on the entire database. Most DBs have this feature, as it is also required for backups.
2. Copy the snapshot to the new follower node
3. The follower connects to the leader and requests all the data changes that have happened since the snapshot was taken. This requires that the snapshot is associated with an exact position in the leader's replication log.
4. When the follower has processed the backlog of data changes since the snapshot, we say it has *caught up*. It can now process data changes from the leader as they happen.


#### Outages
If a follower has an outage, you merely need to create a new follower and copy the latest snapshot from the leader

If the leader has an outage, then a failover procedure is followed. First, the outage is verified by waiting for a timeout period. If the leader fails to respond, then the follower with the most up-to-date data is promoted to leader and all subsequent writes are sent to the new leader node. Of course, this may cause some data loss if there was a discrepancy between the new leader and the old one at the time of the outage.

Failover is fraught with things that can go wrong:
- if asynchronous replication is used, the new leader may not have received all the writes from the old leader before it failed. If the former leader rejoins the cluster after a new leader has been chosen, what should happen to those writes? The most common solution is to simply discard them.
- Discarding writes is especially dangerous if other storage systems outside the database need to be coordinated with the database contents. GitHub once  had a failover and promoted an out-of-date follower to leader. This then caused the counter for primary key to go out of sync with the Redis store, and they ended up showing private data to the wrong users.
- In certain fault scenarios, it could happened that two nodes both believe they are the leader. This is called *Split-Brain* and can be very dangerous. Some systems have a mechanism to shut down one leader if there are multiple, as a safety mechanism.
- What is the right time before a leader is declared dead? A longer timeout means a longer time to recovery in the case where the leader fails. However, if the timeout is too short, there could be unnecessary failovers.

#### Replication Logs
The underlying implementation of replication relies of a _Replication Log_ that stores the state of the leader database. This way, a source is truth is able to be verified and new followers can be quickly replicated. The major implementations are:

- **Statement-Based Replication**: A log of every `INSERT`, `UPDATE`, and `DELETE` command is stored and used as the basis to replicate data. This causes problems with non-deterministic data like using the current time or a random number.  Also there could be problems with commands that increment some value: those commands must be redone exactly as the were originally ordered.
- **Write Ahead Log (WAL)**: An append-only [log](Underlying%20DB%20Data%20Structures.md) of all the writes sent to the leader is used as the basis of replication, used by **PostgreSQL** and **Oracle**. The leader sends a copy across the network to its followers. When a follower processes the log, it builds a data structure identical to the leader. This methods uses low-level details of the DB engine and is thus not backwards compatible.
- **Logical (row based) Log Replication:** A sequence of records describing every write to the database on a row-level. This includes every Insert, update, and delete on a row by row basis. This is used by **MySQL** for its row replication. Since it does not depend on low level engine details, this method is more easily backwards-compatible and even cross-engine compatible.


## Problems with Replication Lag
As the number and distance of replicas grows, a lag occurs between when the leader is written to and when the followers all have the most up to date data. This is what is referred to as **Eventual Consistency**. This becomes an issue when a client sees data backwards in time, especially if it's data that they just updated. There are several ways to solve this:

#### Reading Your Own Writes

![](../Attachments/Pasted%20image%2020221227192731.png)
Say you make a write, then immediately try to read it and you see the old data. Your write went to the leader but the read then went to an stale follower. To ensure that a user always sees the data that they just updated, they can't look at an old replica. Some possible solutions include:
- When reading something that the user may have modified, read it from the leader; otherwise, read it from a follower. This requires that you have some way of knowing whether something might have been modified, without actually querying it.
- If most things in the application are potentially editable by the user, that approach won't be effective, as most things would have to be read from the leader (negating the benefit of read scaling). In that case, other criteria ma be used to decide whether to read from the leader. For example, you could track the time of the last update and, for one minute after the last update, make all reads from the leader. You could also monitor the replication lag on followers and prevent queries on any follower that is more than one minute behind the leader.
- The client can remember the timestamp of its most recent write- then the system can ensure that the replica serving any reads for that user reflects updates at least until that timestamp. If a replica is not sufficiently up to date, either the read can be handled by another replica or the query can wait until the replica has caught up. In this scenario, synchronized clocks become essential
- If your replicas are distributed across multiple datacenters, there is additional complexity. Any request that needs to be served by the leader must be routed to the datacenter that contains the leader.

#### Monotonic Reads

![](../Attachments/Pasted%20image%2020221227192810.png)
If you read from different followers at different stages of replication, you may be data this _back in time_ like comments out of order. Above the user first reads from a fresh replica Follower 1, then reads again from a stale replica Follower 2, and can't see the data they just saw. *Monotonic Reads* guarantees that this kind of anomaly does not happen. One solution is to make sure a user always reads from the same follower, that way they will only see data in a linear time.

#### Consistent Prefix Reads

![](../Attachments/Pasted%20image%2020221227193052.png)
A problem particularly with [DB Partitions](DB%20Partitions.md) is when you read the wrong order of writes coming from different partitions. Above you can see, an observer looking at partition 2 will see that Ms. Poon has time travelled and the conversation is reversed This anomaly requires another type of guarantee, *Consistent Prefix*. This guarantee says that if a a sequence of writers happens in a certain order, then anyone reading those writes will see them appear in the same order.. One solution to this is to make sure that any writes that are causally related to each other are written to the same partition.


## Multi-Leader Replication

![](../Attachments/Pasted%20image%2020220417174437.png)

In some cases, we may want to have multiple leaders. Replication happens much the same way: each node that processes a write must forward that data change to all the other nodes. We call this a *multi-leader* configuration. There are many reasons to use this configuration
- **Multi-datacenter operation**: Imagine you have a database with replicas in several different datacenters (possibly to tolerate a failure at one of the datacenters). With multi-leader, you can have a leader in every database, and route all changes between the leaders over the internet, like in the picture above.
- **Performance:** in single-leader configuration, every write must go over the internet to the single leader. This can add significant latency. Instead, your local datacenter can perform your writes and propagate the changes to the other leaders.
- **Tolerance of Outages and Network Problems**: If an entire datacenter goes down, you can still service write request with no downtime. Also, if connectivity to a datacenter is disrupted, you can easily route requests to other datacenters and they can asynchronously push changes out.
- **Clients with offline operation**: certain applications need to continue while disconnected from the internet. For example, consider your calendar app, even if you are disconnected you can make a meeting (write) and it will sync once you connect to the internet. In this scenario your device is a "leader", every single device is a leader under this scheme, as it allows writes at all times.
- **Collaborative Editing**: Real time *collaborative editing* applications like Google Docs allow multiple people to simultaneously edit a document. When one user edits a document, the changes are instantly applied to their local replica (the state of the document in their web browser or application) and asynchronously duplicated to the server and any other users who are editing the same document.

#### Handling Write Conflicts

![](../Attachments/Pasted%20image%2020220417174434.png)

In a single-leader database, the second writer will either block and wait for the first write to complete, or abort the second write transaction, forcing the user to retry the write. On the other hand, in a multi-leader database, both writes are successful and the conflict is only detected asynchronously at some later point in time. In principle, you could make the conflict detection  synchronous- i.e. wait for the write to be replicated to all replicas before telling the user that the write was successful. However by doing so, you lose the main advantage of Multi-leader: allowing each replica to accept writes independently. 

**Conflict Avoidance**: The best solution to write conflicts is simply *Conflict Avoidance*, trying your best to avoid scenarios where multiple leaders are writing to the same data. In fact, given how poorly most multi-leader implementations handle write conflicts, this is the preferred method.  For example, in an application where a user can edit heir own data, you can ensure that requests from a particular user are always routed to the same datacenter and use the leader in that datacenter for reading and writing. However sometimes you might want to route requests to another datacenter for whatever reason, and then we deal with concurrent writes on different leaders

**Convergence Towards a Consistent State**: If you can't avoid conflict, then you must deal with conflicts and *Converge towards a consistent state*. There are various ways of doing this:
- Give each write a unique ID, pick the write with the highest ID as the _Winner_ and throw away the other writes. If done with a timestamp, this is known as **Last Write Wins (LWW)**, which is very popular but very prone to data loss.
- Give each replica a unique ID, and let writes that originated at a higher-numbered replica always take precedence over writes that originated at a lower-numbered replica. This approach also implies data loss.
- Somehow merge the values together- e.g. order them alphabetically and then concatenate them
- Record the conflict in an explicit data structure that preserves all information, and write application code that resolves the conflict at some later time

You can also write your own custom conflict resolution logic, which may be better suited to your individual problem. This can be done either *On Write* that runs as soon as the system detects a conflict in the log of replicated changes. The code can also execute *On Read*: when a conflict is detected, all the conflicting writes are stored. The next time the data is read, these multiple versions of the data are to the application, which then prompts the user or automatically resolves the conflict.

## Leaderless Replication

There is a third approach, abandoning the concept of a leader and allowing any replica to directly accept writes from clients. Made popular by Amazon's **Dynamo** (note: not AWS DynamoDB which is single-leader) and used by others such as Cassandra, in this method, every node accepts both reads and writes. This allows for potentially limitless scaling, but provides its own challenges with determining the true state of the data at any given time. In some implementations, the client directly send its writes to several replicas, while in others, a coordinator node does this on behalf of the client. This difference in design has profound consequences for the way the database is used.

### Writing to the Database When a Node is Down

![](../Attachments/Pasted%20image%2020221227222612.png)

In a leader configuration, if a node goes down, you would have to go into failover. In leaderless, failover does not exist. The above image shows what happens: the client (user 1234) send the write to all three replicas is parallel, and the two available replicas accept the write but the unavailable replica misses it. Let's say that it's sufficient for two out of three replicas to acknowledge the write: after user 1234 has received two *ok* responses, we consider the write to be successful. The client simply ignores the fact that one of the replicas missed the write. Now the node comes back online except it has *stale* data. To solve this issue, when a client reads from the database, it sends read requests to *several nodes in parallel*.


#### Read Repair & Anti-Entropy Process
In this scheme, eventually all data should be copied to every node to ensure eventual consistency.
- **Read Repair:** when a user makes a request for some data, some of the nodes may contain stale data at which time the fresh nodes will update the stale data. This works well for values that are frequently read.
- **Anti-Entropy Process:** which is a background process that scans for stale data and updates them.   Unlike the replication log in leader-based replication, this *anti-entropy process* does not copy writes in any particular order, and there may be significant delay before the data is copied.


#### Quorums for Reading and Writing

![](../Attachments/Pasted%20image%2020220417181137.png)

Say you make a read or a write and only 2 out of 3 nodes respond that it was successful. Can that be considered a successful operation? In fact, we can determine whether an operation was successful if it reaches a quorum with all the nodes. If there are **N** replicas, every write must be confirmed by **W** nodes to be considered successful, and we must read **R** nodes for each read. As long as $W + R > N$, we expect to get an up-to-date value when reading.

The quorum condition $W + R > N$ allows the system to tolerate unavailable nodes as follows:
- if W < N, we can still process writes if a node is unavailable
- if R < N, we can still process reads if a node is unavailable
- with N=3, W=2, R=2 we can tolerate one unavailable node
- with N=5, W=3, R=3 we can tolerate two unavailable nodes (this is the case in the picture above)
- Normally, reads and writes are always sent to all N replicas in parallel. The parameter W and R determine how many nodes we wait for- i.e. how many of the N nodes need to report success before we consider the read or write to be successful


#### Limitations of Quorum Consistency

The smaller you set W and R, the more likely you are to read stale values, because its more likely that your read didn't include the node with the latest value. On the upside, this configuration allows lower latency and higher availability: if there is a network interruption and many replicas become unreachable, there's a higher chance that you can continue processing reads  and writes. Only after the number of reachable replicas falls below W or R does the database become unavailable for writing or reading, respectively. 

There are some edge cases, however where you can still get stale results even if W+R>N.

- If a **Sloppy Quorum** is used, the W writes can end up on different nodes than the R reads, so there is no longer a guaranteed overlap between the R nodes and the W nodes.
- If two writes occur concurrently, and it is not clear which happened first, then we have to merge the writes. This could be throwing away the last written one, although that could be wrong due to clock skew.
- If a write happens concurrently with a read, the write may be reflected on only some of the replicas. In this case, it's undetermined whether the read returns the old or the new value.
- If a write succeeded on some replicas but failed on others (for example because the disks on some nodes are full), and overall succeeded on fewer than W replicas, it is not rolled back on the replicas where it succeeded. This means that if a write was reported as failed, subsequent reads may or may not return the value from that write.
- If a node carrying a new value fails, and its data is restored from a replica carrying an old value, the number of replicas storing the new value may fall below W, breaking the quorum condition.
- Even if everything is working correctly, there are edge cases in which you can get unlucky with the timing.

Thus you can't take quorum consensus for an absolute guarantee. In practice, Dynamo-style databases are generally optimized for use cases that can tolerate eventual consistency.

#### Monitoring Staleness
For leader-based systems, the database typically exposes metrics for the replication lag. It can get this by comparing the replication log of the leader and followers.

In systems with leaderless replication, there's no fixed order in which writes are applied, which monitoring more difficult. Also, if the database only uses Read-Repair and not Anti-Entropy, there is no limit to how old a value might be. This is also why leaderless is more on the eventual consistency side of things

#### Sloppy Quorums and Hinted Handoffs

In a large cluster (with significantly more than n nodes) it's likely that the client can connect to *some* database nodes during the network interruption, just not to the nodes that it needs to assemble a quorum for a particular value. In that case, you have a decision
- is it better to return errors to all requests for which we cannot reach a quorum of W or R nodes?
- Or should we accept writes anyway, and write them to some nodes that are reachable but aren't among the N nodes on which the value usually lives?

The latter is called *sloppy quorum*: writes and reads still require W and R successful responses, but those may include nodes that are not among the designated "home" node for a value. Once the interruption is fixed, any writes that one node temporarily accepted on behalf of another node are sent to the appropriate "home" nodes. This is called a *hinted handoff*. Sloppy quorums are useful for increasing write availability. As long as W nodes are available, the DB can accept writes. But this means you cannot be sure you're reading fresh data, so its more about durability than consistency.

#### Detecting Concurrent Writes

Concurrent writes are also an issue for leaderless replication, similar to multi-leader. At the end of the day, leaderless can only reach eventual consistency due to its topology, the following are methods to reach that:

- **Last Write Wins**: One approach is to have every replica discard everything except the most recent value and allow older versions to be overwritten. We can force an arbitrary ordering such that the replicas can then converge to a true "most recent update" for all values. LWW achieves eventual convergence, but at the cost of durability: if there are several concurrent writes to the same key, even if they were all reported successful, only one will be declared the winner and the others discarded. If losing data is unacceptable, then LWW is a poor choice of conflict resolution.
- **Merging Siblings**: The database can record the versions of every write. As this is leaderless, there needs to be a version number per replica per key. Each replica increments its own number when processing a write and also keeps track of the version numbers it has seen of the other replicas. This information indicates which values to overwrite and which values to keep as siblings. The collection of versions from all replicas is call a *version vector*. These versions are sent back to the client during reads, and need to be included in writes. Lastly, the database may need to merge writes that have been determined as concurrent, also known as *siblings*. This system makes it safe to write to different replicas, which may result in siblings being created, but no data is lost as long as siblings are merged correctly.