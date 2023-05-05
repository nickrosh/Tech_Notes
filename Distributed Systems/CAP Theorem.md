
CAP is presented as *Consistency, Availability, Partition Tolerance*. Although it sometimes presented as *pick 2 out of the 3*, partition tolerance is a [network fault](Distribution%20Problems.md), you don't choose it.

![](../Attachments/Pasted%20image%2020230129190710.png)

At times when the network is working correctly, a system can provide both consistency ([linearizability](Consistency%20and%20Consensus.md)) and total availability. When a network fault occurs, you have to choose between either linearizability or total availability. Thus a better way of phrasing CAP is *In the presence of a Network Partition (network fault), choose consistency or availability*.

The CAP Theorem as formally defined is of very narrow scope: it only considers one consistency model (namely linearizability) and one kind of fault (*Network Partition* aka nodes that are alive but disconnected from each other). It doesn't say anything about network delays, dead nodes, or other trade-offs.

## PACELC Theorem

In light of the shortcomings of CAP Theorem, a new version has come around that addresses what happens without a network partition. This is called PACELC Theorem

![](../Attachments/Pasted%20image%2020230504141412.png)

If there is a Partition (P), a distributed system can trade between availability and consistency (A & C). Else (E) when a system is running normally in the absence of partitions, the system can tradeoff between latency (L) and consistency (C). The first part of the theorem PAC is the same as CAP Theorem, and the ELC is the extension. The whole thesis is assuming we maintain high availability by replication. So, when there is a failure, CAP theorem prevails. But if not, we still have to consider the tradeoff between consistency and latency of a replicated system.

#### Examples

- Dynamo and Cassandra are PA/EL systems: they choose availability over consistency when a partition occurs, otherwise they choose latency
- BigTable and HBase are PC/EC: they will always choose consistency, giving up availability and lower latency
- MongoDB can be considered PA/EC (default configuration): MongoDB works in a single leader configuration. By default, all writes are performed on the leader. As all [DB Replication](../Databases/DB%20Replication.md) is done asynchronously (from leader to follower), when there is a network partition in which the leader is lost or becomes isolated, there is a chance of losing data that is not replicated to followers, hence there is a loss of consistency during partitions. Alternatively, when MongoDB is configured to write on majority replicas and read from the leader, it would be categorized as PC/EC.