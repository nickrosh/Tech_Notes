
*Consistent Hashing* is a strategy to use when we always want each users requests to go to the same server or database nodes. We can take advantage of this fact and add a [Cache](../Databases/Database%20Cache.md) to each server to have user data immediately available. We can also use this algorithm when using [DB Partitions](../Databases/DB%20Partitions.md) to always route requests to the correct partition.

A naive approach will use a suitable hash function to map the data key to a number. Then, find the server by applying modulo on this number and the total number of servers. For example:

![](../Attachments/Pasted%20image%2020230504210130.png)

This scheme described above solves the problem of finding a server for storing/retrieving the data. But when we add or remove a server, all our existing mappings will be broken. This is because the total number of server will be changed, which was used to find the actual server storing the data. Sop to get things working again, we have to **remap all the keys** and move our data based on the new server count, which will be very difficult.

Distributed systems can use *Consistent Hashing* to distribute data across nodes. Consistent hashing maps data to physical nodes and ensures that only a small set of keys move when servers are added and removed. Consistent hashing stores the data managed by a distributed system in a ring. Each node in the ring is assigned a range of data. Here is an example of the consistent hash ring:

![](../Attachments/Pasted%20image%2020230504210512.png)

With consistent hashing, the ring is divided into smaller, predefined ranges. Each node is assigned one of these ranges. The start of the range is called a *token*. This means that each node will be assigned one token. Whenever the system needs to read or write data, the first step it performs is to apply the MD5 hashing algorithm to the key (or any other hash). The output of this hash determines within which range the data lies and hence, on which node the data will be stored. As we saw above, each node is supposed to store data for a fixed range. Thus, the hash generated from the key tells us the node where the data will be stored:

![](../Attachments/Pasted%20image%2020230504211012.png)

Consistent hashing also works when you want a particular user to always go to a certain server. Even the [IP](../Internet%20&%20Networking/IP.md) or any other identifying data can be mapped to the circle to ensure a user will always go to the same server, which would greatly increase the efficiency of a [Database Cache](../Databases/Database%20Cache.md).


![](../Attachments/Pasted%20image%2020230202222355.png)

If a [server fails](Distribution%20Problems.md), then the users that used to call that server will now be redirected to the server that was "clockwise" from the failed one, and all the other requests stay as they are. In the above picture, if server "A" failed, then Casey would be redirected to server "D", or in the data partition use case, the data would be replicated to the new server. However, this scheme can result in non-uniform data and load distribution. This problem can be solved with virtual nodes

## Virtual Nodes

Adding and removing nodes in any distributed system is quite common. Existing nodes can die and may need to be decommissioned. Similarly, new nodes may be added to an existing cluster to meet growing growing demands. 

As we saw above, the basic Consistent Hashing algorithm assigns a single token (or a consecutive hash range) to each physical node. This was a static division of ranges that requires calculating tokens based on a given number of nodes. This scheme made adding or replacing a node an expensive operation. Here are a few potential issues with a manual and fixed division of the ranges:
- **Adding or Removing Nodes**: This will result in re-computing the tokens causing a significant administrative overhead for a large cluster
- **Hotspots**: Since each node is assigned one large range, if the data is not evenly distributed, some nodes can become hotspots
- **Node Rebuilding**: Since each node's data might be replicated (for fault tolerance) on a fixed number of other nodes, when we need to rebuild a node, only its replica nodes can provide the data. This puts a lot of pressure on the replica nodes and can lead to service degradation.

To handle these issues, we can use a different scheme. Instead of assigning a single token to a node, the hash range is divided into multiple smaller ranges, and each physical node is assigned several of these smaller ranges. Each of these subranges is considered a Virtual Node, or Vnode. With Vnodes, instead of a node being responsible for just one token, it is responsible for many tokens (or subranges).

![](../Attachments/Pasted%20image%2020230504212056.png)

Vnodes give the following advantages:
1. As Vnodes help spread the load more evenly across the physical nodes on the cluster by dividing the hash ranges into smaller subranges, this speeds up the rebalancing process after adding or removing nodes. When a new node is added, it receives many Vnodes from the existing nodes to maintain a balanced cluster. Similarly, when a node needs to be rebuilt, instead of getting data from a fixed number of replicas, many nodes participate in the rebuild process.
2. Vnodes make it easier to maintain a cluster containing heterogeneous machines. We can assign subranges proportional to the compute power of a machine
3. In contrast to one big range, since Vnodes help assign smaller ranges to each physical node, this decreases the probability of hotspots.


## Data Replication using Consistent Hashing

To ensure high [availability and durability](Design%20Requirements.md), consistent hashing replicates each data item on multiple N nodes in the system where the value N is equivalent to the replication factor.

> The replication factor is the number of nodes that will receive the copy of the same data. For example, a replication factor of 2 means there are 2 copies of each data item.

Each key is assigned to a coordinator node (generally the first node that falls in the hash range), which first stores the data locally and then replicates it to N - 1 clockwise successor nodes on the ring. This results in each node owing the region on the ring between it and its Nth predecessor. In an eventually consistent system, this replication is done asynchronously (in the background)

![](../Attachments/Pasted%20image%2020230504212729.png)
