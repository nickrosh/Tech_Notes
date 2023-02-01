
If we cannot allow an entire distributed system to fail whenever there is a fault, we need to build systems that can tolerate faults and [Distribution Problems](Distribution%20Problems.md). The best way of building fault-tolerant systems is to find some general-purpose abstractions with useful guarantees, implement them once, and then let applications rely on those guarantees. This is the same approach used with [Transactions](../Databases/Transactions.md). By using transactions, the application can pretend that there are no crashes ([atomicity](../Databases/ACID.md)), that nobody else is concurrently accessing the database (isolation), and that storage devices are perfectly reliable (durability).

Most [replicated](../Databases/DB%20Replication.md) databases provide at least *eventual consistency* which means that if you stop writing to the database, then eventually all read requests will return the same value. In other words, every node will converge. But this is a weak guarantee, we need stronger guarantees to be able to use an application properly.


## Linearizability

Imagine if two people are looking at their phone during a football game. One of the teams score and they check their phones, and only one of them sees that the score has been updated. Even though they had refreshed at the same time, one of them got a stale result. This is demonstrated in the image below.

![](../Attachments/Pasted%20image%2020230129173034.png)

The basic idea behind *Linearizability* is simple: make the system appear as if there is only a single copy of the data. In a linearizable system, as soon as one client successfully completes a write, all clients reading from the database must be able to see the value just written. Maintaining the illusion of a single copy of the data means guaranteeing that the value read is the most recent, up-to-date value, and doesn't come from a stale cache or [replica](../Databases/DB%20Replication.md). In other words, linearizability is a *recency guarantee*.

Linearizability is easily confused with [serializability](../Databases/Transactions.md). Serializability is an isolation property of *transactions*, where every transaction may read and write multiple objects, and guarantees that they will behave as though they were executed *serially*. Linearizability is a recency guarantee on reads and writes of an object. It doesn't group operations together into transactions, so it does not prevent problems like write skew. A DB can provide both serializability and linearizability.

#### Relying on Linearizability

A result that is outdated by a few seconds is not likely to be a huge issue, but there are some situations where linearizability is an important requirement. A system that uses [single-leader replication](../Databases/DB%20Replication.md) needs to ensure that there is only one leader, not several (split brain). One way of electing a leader is to use a lock: every node that starts up tries to acquire the lock, and the one that succeeds becomes leader. No matter how this lock is implemented, it must be linearizable. There is also uniqueness constraints, for example a unique username or email address for each user, and in file storage each file needs a unique path and name. This is similar to a lock: when a user registers for a service, they acquire a "lock" on a chosen username. Hard uniqueness constraints in [Relational Databases](../Databases/Relational%20Databases.md) require linearizability.

![](../Attachments/Pasted%20image%2020230129182007.png)

When there are multiple communication channels, we can run into race conditions with linearizability. For example in the image above, the image resizer needs to be explicitly instructed to perform a resizing job, and this instruction is sent from the web server to the resizer via a [Message Queue](Message%20Queue.md). If the system is linearizable, this will work fine. If not, there is a risk of a race condition: the message queue (steps 3&4) might be faster than the internal replication, in this case when the resizer fetches the image (step 5). It might see an old version of the image, or nothing at all. If it processes an old image, the full sized and resized versions of your DB will be permanently inconsistent.

#### Implementing Linearizable Systems

Since linearizability essentially means "behave as though there is only a single copy of the data, and all operations on it are atomic", the simplest answer would be to actually use a single copy of the data. However that is not scalable. Different replication strategies can be made linearizable:
- **Single-Leader Replication (potentially linearizable)**: The leader has the primary copy of data and followers use backups. Thus using the leader for reads or from synchronously updated followers can be linearizable (when not using snapshot isolation). Using the leader for reads relies on the assumption that you know who the leader is, this can also be a problem
- **Consensus Algorithm (linearizable)**: Some consensus algorithms, which we discuss below, bear a resemblance to single-leader replication. However, consensus protocols contain measures to prevent split brain and stale replicas. Thanks to these details, it can be safely linearizable.
- **Multi-Leader Replication (not linearizable)**: The multiple leaders and nodes can product conflicting writes that leads to resolution. Such conflicts are an artifact of not having a single copy of the data
- **Leaderless Replication (not linearizable)**: People sometimes claim that you can obtain strong consistency by requiring quorum reads and writes (w + r > n), but this is not quite true. Race conditions can occur that made reads stale. You can make leaderless replication linearizable with read repair, but this is a performance hit and not commonly done.

#### The Cost of Linearizability

![](../Attachments/Pasted%20image%2020230129185437.png)

Consider what happens when there is a [network interruption](Distribution%20Problems.md) between two datacenters (above image). If the network between datacenters is interrupted in a single-leader setup, clients connected to follower datacenters can't contact the leader, so they cannot make any writes to the database, nor any linearizable reads. They can still make reads from the follower, but they might be stale. With a multi-leader database, each datacenter can continue operating normally since writes from one datacenter are asynchronously replicated to the other, the writes are simply queued up and exchanged when network connectivity is restored.

This issue is not just a consequence of single-leader and multi-leader replication, any linearizable database has this problem, no matter how it is implemented.
- If your application *requires* linearizability, and some replicas are disconnected from the other replicas due to a network problem, then some replicas cannot process requests while they are disconnected: they must either wait until the network problem is fixed, or return and error (either way, they become *unavailable*).
- If your application *does not require* linearizability, then it can be written in a way that each replica can process requests independently, even if it is disconnected from other replicas (e.g. multi-leader). In this case, the application can remain *available* in the face of a network problem, but its behavior is not linearizable.

This insight is popularly known as the [CAP Theorem](CAP%20Theorem.md). In reality, linearizability is not used very often because it brings significant performance delays. Linearizability is slow, and this is true all the time, not just during a fault.


## Ordering Guarantees

*Causality* imposes an order on events: cause comes before effect.. A message is sent before that message is received; the question comes before the answer. And, like in real life, one thing leads to another: one node reads some data and then writes something as a result, another node reads the thing that was written and writes something else in turn, and so on. These chains of causally dependent operations define the causal order in the system, i.e. what happened before what.

A *total order* allows any two elements to be compared. However, sets like {a, b} and {c, d} cannot be compared, because one is not a subset of the other. Therefore sets are *partially ordered*: in some cases one set is greater than another (if one set contains all the elements of another), but in other cases they are incompatible. 
- **Linearizability**: In a *Linearizable* system, we have a *total order* of operations: if the system behaves as if there is only a single copy of the data, and every operation is atomic, this means that for any two operations we can always say which one happened first.
- **Causality**: Two operations are concurrent if neither happened before the other. In other words, two events are ordered if they are causally related, but they are incomparable if they are concurrent. This means that causality defines a *partial order*, not a total order: some operations are ordered with respect to each other, but some are incomparable.

Linearizability is stronger than causal consistency. Linearizability *implies* causality: any system that is linearizable will preserve causality correctly. However this is not the only way, you can have a causally consistent system that does not suffer the performance penalty of linearizability.

#### Sequence Number Ordering

Actually tracking all causal dependencies can be impractical. There is a better way: we can use *sequence numbers* or *timestamps* to order events. A timestamp need not come from a [time-of-day clock](Distribution%20Problems.md), it can come from a *logical clock*, which is an algorithm to generate a sequence of numbers to identify operations, typically using counters that are incremented for every operation.

There is actually a simple method of generating sequence numbers that is consistent with causality. It is called a *Lamport timestamp*. Each Node has a unique identifier, and each node keeps a counter of the number of operations it has processed. The Lamport timestamp is then simply a pair of (*counter*, *node ID*). Every node and every client keeps track of the *maximum counter* value it has seen so far, and includes that maximum on every request. When a node receives a request or response with a maximum counter values greater than its own counter value, it immediately increases its own counter to that maximum.

Although Lamport timestamps define a total order or operations that is consistent with causality, they have many problems. For example, if two users try to make an account with the same username concurrently, one should fail. In order to be sure that no other node is in the process of concurrently creating an account with the same username and a lower timestamp, you would have to check every single node. If one of the other nodes [can't be reached](Distribution%20Problems.md), the entire system would grind to a halt.

#### Total Order Broadcast

Single-Leader Replication determines a total order of operations by choosing one node as the leader and sequencing all operations on a single CPU core on the leader. The challenge then is how to scale the system if the throughput is greater than a single leader can handle, and also how to handle failover if the leader fails. This problem is known as *total order broadcast* or *atomic broadcast*. Total order broadcast us usually described as a protocol for exchanging messages between nodes. Informally, it requires two safety properties always be satisfied:
- **Reliable Delivery**: No messages are lost, if a message is delivered to a node, it is delivered to all nodes.
- **Totally Ordered Deliver**: messages are delivered to every node in the same order.

Total order broadcasting is exactly what is needed for replication: if every message represents a write to the database, and every replica processes the same writes in the same order, then the replicas will remain consistent with each other. Similarly, total order broadcast can be used to implement serializable transactions: if every message represents a deterministic transaction to be executed as a stored procedure, and if every node produces those messages in the same order, then the partitions and replicas of the database are kept consistent  with each other. Another way of looking at total order broadcasting is as a *log*. Delivering a message is like appending to the log. Since all nodes must deliver the same messages in the same order, all nodes can read the log and see the same sequence of messages. It can be proved that a linearizable compare-and-set operation and total order broadcast are both *equivalent to consensus*.  


## Distributed Transactions and Consensus

Consensus is one of the most important problems in distributed computing. The goal is simply to *get several nodes to agree on something*. There are a number of situations where it is important for nodes to agree. For example:
- In a database with Single-Leader Replication, all nodes need to agree on which  node is the leader. This leadership problem can become contested if some nodes can't communicate with others due to a network fault. Consensus is needed to prevent split brain.
- In a database that supports transactions spanning several nodes or partitions, we have the problem that a transaction may fail on some nodes but succeed on others. If we want to maintain transaction atomicity (in the sense of [ACID](../Databases/ACID.md)) we have to get all nodes to agree on the outcome of the transaction.

#### Atomic Commit & Two-Phase Commit (2PC)

For transactions that execute on a single database node, atomicity is commonly implemented by the storage engine. When the client asks the DB to commit the transaction, the database makes the transactions writes durable (typically with [write ahead logs](../Databases/Underlying%20DB%20Data%20Structures.md)) then appends the a commit record to the log on disk. If there is a crash, the transaction is recovered from the log. If a commit record has been added, that means the transaction successfully went through, if not then all writes associated with the transaction are rolled back. Thus, on a single node, transaction commitment crucially depends on the *order* in which data is durably written to disk: first to disk then to the commit record.

What if we want transactions distributed over multiple nodes? Most [NoSQL](../Databases/NoSQL.md) databases do not allow this functionality, but various clustered [Relational Databases](../Databases/Relational%20Databases.md) do. In these cases, it is not sufficient to just send a commit request to all of the nodes and independently commit the transaction on each node. If it succeeds in some and fails in others, this violates the atomicity guarantee.

*Two-Phase Commit* (2PC) is an algorithm for achieving atomic transaction commit across multiple nodes. Note: don't confuse 2PC with [Two-Phase Locking](../Databases/Transactions.md) which guarantees serializability. 2PC is shown below:

![](../Attachments/Pasted%20image%2020230130230143.png)

2PC uses a new component not used in single-node [Transactions](../Databases/Transactions.md): a *coordinator* (also known as a *transaction manager*). A 2PC transaction begins with the application reading and writing data on multiple database nodes, as normal. We call these nodes *participants* in the transaction. When the application is ready to commit, the coordinator begins phase 1: it sends a *prepare* request to each of the nodes, asking them whether they are able to commit. The coordinator then tracks the responses from the participants.
- If all participants reply "yes" indicating they are ready to commit, then the coordinator send out a *commit* request in phase 2, and the commit actually takes place.
- If any of the participants replies "no" the coordinator sends an *abort* request to all nodes in phase 2.

This process is somewhat like a marriage ceremony. The priests asks the bride and groom individually whether each wants to marry the other, and typically receives the answer "I do" from both. If both say yes, the marriage is done: the transaction is committed. To understand further why 2PC works, we have to break down the process:
1. When the application wants to begin a distributed transaction, it requests a transaction ID from the coordinator. This transaction ID is globally unique.
2. The application begins single-node transaction on each of the participants and assigns them the transaction ID. If anything goes wrong at this stage (e.g. node crashes or a request time out), the coordinator or any of the participants can abort.
3. When the application is ready to commit, the coordinator send a prepare request to all participants tagged with the transaction ID. If any of these requests fails or times out, the coordinator sends an abort request to all participants.
4. When a participant receives a prepare request, it makes sure that it can definitely commit the transaction under all circumstances. By answering "yes" to the coordinator, the node promises to commit the transaction without error if requested. In other words, the participant surrenders the right to abort the transaction, but without actually committing it (if anyone objects to this marriage, speak now or forever hold your peace)
5. When the coordinator has received responses to all prepare requests, it makes a definitive decision on whether to commit or abort the transaction (committing only if all participants said "yes"). The coordinator must write that decision to its transaction log on disk so that it knows which way it decided in case it subsequently crashes. This is called the *commit point*
6. Once the coordinator's decision has been written to disk, the commit or abort request is sent to all participants. If this request fails or times out, the coordinator must retry forever until it succeeds. There is no going back: if the decision was to commit, that decision must be enforced, no matter how many retires it takes. If a node crashes, the transaction will be committed when it recovers.

Thus the protocol contains two crucial "points of no return": when a participant votes "yes" it promises that it will definitely be able to commit later, and once the coordinator decides, that decision is final. Those promises ensure the atomicity of 2PC. This algorithm contains an Achilles Heel. If the coordinator itself crashes during a commit, and only some of the participants have received the commit instructions, the rest will not know what to do. The only way 2PC can complete is by waiting for the coordinator to recover. This is why the coordinator must write its commit or abort decision to a transaction log on disk before sending commit or abort requests to participants: when the coordinator recovers, it determines the status of all in-doubt transactions by reading its transaction log. Any transactions without a commit record are aborted. Thus, the commit point of 2PC comes down to a regular single-node atomic commit on the coordinator.

#### Distributed Transactions in Practice

2PC comes with significant performance penalties, so in real systems it has a mixed reputation. Although we must differentiate between the two types of distributed transaction:
- **Database-Internal distributed transactions**: Some distributed databases support internal transactions among the nodes of that database, like Volt DB and MySQL Cluster's NDB storage engine.
- **Heterogeneous distributed transactions**: where the participants are tow or more different technologies. For example, two different databases from different vendors, or even non-databases like message brokers. A distributed transaction across these systems must ensure atomic commit, even though the systems may be entirely different under the hood.

XA transactions is a standard for implementing 2PC across heterogeneous technologies. XA assumes that your application uses a network driver or client library to communicate with the participant databases or messaging services. The drives exposes callbacks through which the coordinator can ask the participant to prepare, commit, or abort. If the application process crashes, or the machine on which the application is running dies, the XA coordinator goes with it. This is significant as *locking* means that the databases are holding locks on every row that is in limbo of commit or abort. If the coordinator is down, the data could be locked for an extended period of time. In theory, a crashed coordinator should cleanly restart and recover its state from the log and resolve any in-doubt transactions. In practice *orphaned* or in-doubt transactions occur. It is up to the administrator to manually decide what to do with the transaction. Many XA implementations have an emergency escape hatch called *heuristic decisions* allowing a participant to unilaterally decide to abort or commit an in-doubt transaction without definitive decision from the coordinator.

XA transactions solve the real and important problem of keeping several participant systems consistent with each other, but as we see they also add significant operational burden. The key realization is that the coordinator itself is like a database, so it needs to be approached with care:
- If the coordinator is not replicated, it is a single point of failure for the entire system.
- Many server-side apps are developed to be stateless (as favored by [HTTP](../Internet%20&%20Networking/HTTP.md)). When a coordinator is added to the application, its log becomes a critical part of the durable system state. The app is no longer stateless
- Since XA needs to be compatible with a wide range of data systems, it is necessarily a lowest common denominator

#### Fault Tolerant Consensus

Informally, consensus means getting several nodes to agree on something. For example, if several people concurrently try to book the last sear on an airplane, or the same seat in a theater, then a consensus algorithm could be used to determine which one of these mutually incompatible operations should be the winner. The problem is formalized as the following: one or more nodes may *propose* values, and the consensus algorithm *decides* on one of those values. It must satisfy the following properties:
- **Uniform Agreement**: No two nodes decide differently
- **Integrity**: No node decides twice
- **Validity**: If a node decides value v, then v was proposed by some node
- **Termination**: Every node that does not crash eventually decides some value.

If you don't care about fault-tolerance then this is easy, you just declare one node to always be right. In fact, this is actually what 2PC is like, as the coordinator is the single-point of failure. Termination formalizes fault-tolerance, it says a consensus algorithm cannot simply sit around and do nothing forever, it must make progress.

Real life fault-tolerant consensus algorithms mostly use decide on a *sequence* of values, which makes them *total order broadcast* algorithms. Remember that total order broadcast is requires messages to be delivered exactly once, in the same order, to all nodes. This is the same as performing multiple rounds of consensus.

Single-leader replication has a leader that takes all the writes and applies them to the followers in the same order, thus keeping replicas up to date. Isn't this essentially Total Order Broadcast? One difference, the leader must be chosen automatically by consensus to be fault tolerant.

Every time the current leader is thought to be dead, a vote is started among the nodes to elect a new leader. This election is given an incremented *epoch number*, and thus epoch numbers are totally ordered and monotonically increasing. If there is a conflict between two different leaders in two different epochs, the leader with the higher number wins.

The leader must collect votes from a *quorum* of nodes. For every decision that a leader wants to take. Nodes only vote in favor of a proposal if they are not aware of any other leader with a higher epoch number. Thus, we have two rounds of voting. One for electing the leader, and another for any proposal in order to make sure there is only one leader. This is superficially similar to 2PC, except in 2PC the coordinator is not elected, and requires "yes" from *every* participant. Consensus  quorums generally only need simple majorities.

Consensus is very powerful, but comes with drawbacks. As it always needs strict majority, if a quorum of nodes are not online, the entire system will have to wait until more restart. Consensus algorithms rely on timeouts to detect failed nodes, thus in highly variable networks, this can significantly hurt performance. Consensus algorithms are particularly sensitive to network problems.

#### Membership and Coordination Services

Projects like Zookeeper or ETCD are often describes as "coordination and configuration services" and "distributed key-value stores". They are designed to hold small amounts of data to fit into [Memory & Cache](../Electrical%20Engineering/Digital/Memory%20&%20Cache.md), to manage other projects that rely on it like Kafka, Hadoop, HBase, etc. The small amount of data is replicated across all the nodes using a fault-tolerant total order broadcast algorithm. Zookeeper implements not only total order broadcast, but also other features that make it especially useful when building distributed systems:
- **Linearizable atomic operations**: using an atomic compare-and-set operation, you can implement a lock, only one operation at a time. The consensus protocol guarantees that the operation will be atomic and linearizable, even if a node fails or the network is interrupted at any point. The lock is usually implemented as a *lease* with a time of expiration.
- **Total ordering of operations**: As mentioned before, when some resource is protected by a lock, you need a token to prevent clients from conflicting with each other in the case of a process pause. The fencing token is some monotonically increasing number
- **Failure detection**: Clients maintain a long-lived session on Zookeeper servers, and the client and server periodically exchange heartbeats to check that the other node is still alive. If the connection is interrupted of a Zookeeper node fails, the session remains alive. The heartbeat will tell Zookeeper if a node is dead.
- **Change notifications**: Not only can one client read locks and values that were created by another client, but it can also watch them for changes. Thus a client can find out when another client joins the cluster, or if another client fails. By subscribing to notifications, a client avoids having to frequently poll to find out about changes.

Zookeeper and other similar applications are also used for *service discovery*. That is, to find out which [IP](../Internet%20&%20Networking/IP.md) addresses you need to connect to in order to reach a particular server In could operations, where it cis common for virtual machines to continually come and go, you often don't know the IP addresses ahead of time. This is as opposed to traditional [DNS](../Internet%20&%20Networking/DNS.md) which reads multiple caching layers to reach good performance and availability. Zookeeper is very useful as a *membership service* that tells you which nodes are alive and which ones are dead. Even if one is not really dead, the fact that the system considers it dead is what's important.