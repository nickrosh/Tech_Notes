
Working with distributed systems is fundamentally different from writing software on a single computer, and the main difference is that there are lots of new and exciting ways for things to go wrong.

## Faults and Partial Failures

When working with a single computer, software will work until it doesn't. The result is *deterministic*, the same input will always yield the same output. When we deal with distribution, the situation is different. There may be some parts that are broken and some parts that are not broken. This is known as a *partial failure*. The difficulty is that partial failures are *nondeterministic*: if you try to do something requiring multiple nodes it may sometimes work, and other times fail.

#### Cloud Computing and Supercomputing

There are competing philosophies on how to build large-scale computing systems:
- At one end of the spectrum is the field of *High-Performance Computing* (HPC). Super computers with thousands of [CPUs](../Electrical%20Engineering/Digital/CPU.md) that are typically used for computationally intensive tasks.
- The other end of the extreme is *Cloud Computing*, which is often associated with multi-tenant datacenters, commodity computers connected with an [IP](../Internet%20&%20Networking/IP.md) Network (often Ethernet), elastic/on-demand resource allocation, and metered billing.
- Traditional enterprise datacenters usually lie somewhere between these extremes.

With these philosophies comes very different ways of handling faults. In a supercomputer, a job typically checkpoints the state of tis computation to durable storage from time to time. If one node fails, a common solution is to simply stop the entire cluster workload. After the faulty node is repaired, the computation is restarted from the last checkpoint. Thus, a supercomputer is more like a single-node computer than a distributed system.

When discussing Distributed Systems, there are many differences with Supercomputers:
- Many internet-related applications are *online*, in the sense that they need to be able to serve users with low latency at any time.
- Supercomputers are typically built with custom hardware. On the other hand, nodes in cloud services are built from commodity machines
- Large Datacenter networks are based on [IP](../Internet%20&%20Networking/IP.md) and Ethernet, arranged in close topologies. Supercomputers often use specialized network topologies to maximize computation throughput for the specific purposes.
- The bigger a system gets, the more likely it is that one of its components is broken. In a systems with thousands of nodes, it is reasonable to assume *something* is always broken, thus you can't just give up and start over for every fault.
- Systems that can tolerate failed nodes and can still work are very useful. This also allows things like rolling updates without service interruption.

If we want to make distributed systems work, we must accept the possibility of partial failure and build fault-tolerance mechanisms into the software. In other words, we need to build a reliable system from unreliable components. In the same way that [IP](../Internet%20&%20Networking/IP.md) packets are unreliable, but [TCP](../Internet%20&%20Networking/TCP.md) makes it a reliable system.

## Unreliable Networks

The internet and most internal networks in datacenters (often Ethernet) are *asynchronous packet networks*. In this kind of network, one node can send a message (a packet) to another node, but the network gives no guarantees as to when it will arrive, or whether it will arrive at all. If you send a request and expect a response, many things could go wrong:
1. Your request may have been lost (perhaps someone unplugged a network cable)
2. Your request may be waiting in a [Message Queue](Message%20Queue.md) and will be delivered later (perhaps the network or the recipient is overloaded)
3. The remote node may have failed (perhaps it crashed or it was powered down).
4. The remote node may have temporarily stopped responding (perhaps it is expecting a long [Garbage Collection](../Systems%20Software/Garbage%20Collection.md) pause) but it will start responding later
5. The remote node may have processed your request, but the response has been lost on the network (perhaps a network switch was misconfigured)
6. The remote node may have processed your request, but the response has been delayed and will be delivered later (perhaps the network or your machine is overloaded)

The usual way of handling this issue of not knowing what's wrong is a *timeout*: after some time you give up waiting and assume that the response is not going to arrive. However, when a timeout occurs, you still don't know whether the remote node got your request or not (and if the request is still queued somewhere, it may still be delivered to the recipient, even if the sender has given up on it).

#### Detecting Faults

Many system need to automatically detect faults:
- A [Load Balancer](Load%20Balancer.md) needs to stop sending requests to a node that is dead 
- In a distributed database with [Single-Leader Replication](../Databases/DB%20Replication.md), if the leader fails, one of the followers needs to be promoted to replace the leader.

Unfortunately, the uncertainty of the network makes it difficult to tell whether a node is working or not. In some specific circumstances you might get some feedback to explicitly tell you something is not working:
- If you can reach the machine on which the node should be running, but no process is listening on the destination port (e.g. the program crashed), the [Operating System](../Systems%20Software/Operating%20System.md) will close or refuse [TCP](../Internet%20&%20Networking/TCP.md) connections. However, if the node crashed while handling your request, you have no way of knowing how much data was processed.
- If a node process crashed but the node's operating system keeps running, a script can notify other nodes about the crash so another one can take over without waiting for timeout
- If you have access to the management interface of the network switch, you can query them to detect link failures at a [hardware level](../Internet%20&%20Networking/OSI%20Model.md).
- If a router is sure that the [IP](../Internet%20&%20Networking/IP.md) address you're trying to connect to is unreachable, it may reply to you with an *ICMP Destination Unreachable* packet, however the router knows as much as the other nodes in the network.

If you want to be sure that a request was successful, you need a positive response from the application itself.

#### Timeouts and Unbounded Delays

If a timeout is the only sure way of detecting a fault, then how long should the timeout be? There is unfortunately no simple answer.


#### Synchronous and Asynchronous Networks


## Unreliable Clocks