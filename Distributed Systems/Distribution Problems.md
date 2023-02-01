
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

If a timeout is the only sure way of detecting a fault, then how long should the timeout be? There is unfortunately no simple answer. A long timeout means a long wait until a node is declared dead. A short timeout detects faults faster, but carries a higher risk of incorrectly declaring a node dead when in fact it has only suffered a slowdown. Premature termination can be problematic as the node may still try to send messages, even though it was declared "dead".

Most systems we work with do not have bounded time delays that we know about beforehand. Asynchronous networks have *unbounded delays*, which means they try to deliver packets as quickly as possible, but there is no upper limit on the time it may take for a packet to arrive. Most server implementations cannot guarantee that they can handle requests within some maximum time. It will only take one transient spike to throw a system off balance.

![](../Attachments/Pasted%20image%2020230127125611.png)

Most often, the variability of packet delays on computer networks are due to queueing:
- If several nodes simultaneously try to send packets to the same destination, the network switch must queue them up and feed them into the destination network link one by one (image above). If the network is busy, a packet may have to wait to get a slot. If the switch queue fills up entirely, the packet will be lost even though everything is working fine.
- When a packet reaches a machine, If all [CPU](../Electrical%20Engineering/Digital/CPU.md) cores are currently busy, the incoming request from the network is queued by the [Operating System](../Systems%20Software/Operating%20System.md) until the application is ready to handle it. This may take an arbitrary amount of time
- In virtualized environments, a running OS is often paused for tens of milliseconds while another VM uses a CPU core. During this time, incoming data is queued (buffered) by the VM.
- [TCP](../Internet%20&%20Networking/TCP.md) performs *flow control* (also known as *congestion avoidance* or *backpressure*) in which a node limits its own rate of sending in order to avoid overloading a network link. Moreover, TCP will consider a packet lost if it is not acknowledged within some timeout, which is calculated from observed round-trip times.

In public clouds and multi-tenant datacenters, resources are shared among many customers: the network links and switches, and even each machine's networking interface and CPUs are shared. [Batch workloads](Batch%20Processing.md) such as MapReduce can easily saturate network links, as you have no control over other customer's usage. In such environments, you can only choose timeouts experimentally: measure the distribution of network round-trip times over an extended period, and over many machines to determine the expected variability of delays. Even better, rather than using configured constant timeouts, systems can continually measure response times and their variability (*jitter*) and automatically adjust timeouts according to the observed response time distribution.

## Unreliable Clocks

Clocks and time are very important. Applications depend on clocks for various reasons:
1. Has this request timed out yet?
2. What's the 99th percentile response time of this service?
3. How many queries per second did this service handle on average in the last five minutes?
4. How long did the user spend on our site?
5. When was this article published?
6. At what date and time should the reminder email be sent?
7. What is the timestamp on this error message in the log file?

1-4 describe *durations* and the rest describe *points in time*. Time is a tricky business as network communication is not instantaneous, and there are variable delays. Modern Computers have two types of clocks:
- **Time of Day Clocks**: Performs as you would expect from a regular clock. It returns the current date and time according to some calendar. Time of Day Clocks are usually synchronized with *Network Time Protocol* (NTP) which allows the computer clock to be adjusted according to the time reported by a group of servers. If a computer's clock is too far ahead of the NTP, it will reset and "jump" ahead in time. These jumps make time-of-day clocks unsuitable for measuring elapsed time
- **Monotonic Clocks**: These are suitable for measuring a duration (time interval), such as a timeout or a service's response time. Monotonic clocks are always guaranteed to advance with time, unlike time-of-day clocks, which may jump backwards. NTP may adjust the frequency at which monotonic clocks move forward if it detects the computer's local quartz is moving faster or slower than the NTP.


#### Clock Synchronization and Accuracy

Monotonic clocks don't need synchronization, but regular clocks do. Time-of-Day clocks are periodically compared against the NTP and synchronized to match the NTP. Hardware clocks and even NTP can have many problems:
- The quartz clock in a computer is not very accurate, it *drifts*. Clock drift varies depending on the temperature of the machine. Google assumes a clock drift of 6ms drift for a clock that is resynchronized every 30 seconds, or 17 seconds if done once a day.
- If a computer's clock differs too much from an NTP server, it may refuse to synchronize, or the local clock will be forcibly reset. Any applications observing the time may see the time jump ahead or backwards
- If a node is accidentally firewalled off from NTP servers (*sounds like a best practice*), the misconfiguration may go unnoticed for some time. 
- NTP synchronization can only be as good as the network delay, so there is a limit to its accuracy. Large network delays can cause errors in the NTP synchronization, with errors of at least 35ms.
- Some NTP servers are wrong or misconfigured, so group consensus is required
- Lead seconds result in a minute that is 59 or 61 seconds long, which messes up timing assumptions in systems that are not designed to encounter leap seconds.
- In Virtual Machines, the hardware clock is virtualized, which raises additional challenges for applications that need accurate timekeeping.
- If you run software on devices that you don't fully control (e.g. mobile or embedded devices), you probably can't trust the devices clock at all.

#### Process Pauses

![](../Attachments/Pasted%20image%2020230127153316.png)

Even if our software only depends on the local monotonic clock, there is another problem: the program assumes that very little time passes between the point that it checks the time and the time when the request is processed. Imagine a lock is needed to write data, and the first client takes it then has a process pause that causes the lock to expire without the user realizing. This is demonstrated in the image above. Is it crazy to assume that a thread might be paused for so long? There are many reasons why this would happen:
- Many programming language runtimes (JVM, Go, etc.) preform [Garbage Collection](../Systems%20Software/Garbage%20Collection.md) (GC) that occasionally needs to stop all running threads. These "stop-the-world" GC pauses have sometimes been known to last for several minutes! Although the pauses can often be reduced by changing allocation patterns or tuning GC settings, we must assume the worst if we want to offer robust guarantees.
- In virtualized environments, a virtual machine can be *suspended* (pausing all execution of [Processes & Threads](../Systems%20Software/Processes%20&%20Threads.md) and saving the contents of [Memory](../Electrical%20Engineering/Digital/Memory%20&%20Cache.md) to disk) and *resumed* (restoring the contents of memory and continuing execution). This process can take an unknown amount of time. This feature is sometimes used for *live migration* of virtual machines from one host to another without a reboot.
- On end-user devices, the process can be interrupted arbitrarily, like a user closing a laptop
- When the operating system context-switches to another thread, or when the hypervisor switches to a different virtual machine, the current running thread can be paused at any arbitrary point in the code. In the case of a VM, the CPU time spent in other VMs is known as *steal time*. It may take some time for the paused thread to resume.
- If the application performs asynchronous disk access, a thread may be paused waiting for a slow disk I/O operation to complete. In many languages, disk access can happen surprisingly, even if the code doesn't explicitly mention file access.
- If the OS is configured to allow *swapping to disk (paging*, a simple memory access may result in a page fault that requires a page from disk to be loaded into memory. The thread is paused while this slow I/O operation takes place. If memory pressure is high, this may in turn require a different page to be swapped to disk. In the worst cases, you do more swapping than actual work in a situation known as *page thrashing*. To avoid this problem, paging is often disabled on server machines.
- A Unix process can be stopped arbitrarily with `SIGSTOP`, or Ctrl-Z in a shell.

All these situations stop the running thread at any point and resume at a later point. A node is a distributed system must assume that its execution can be paused for a significant length of time at any point, even in the middle of a function. When using locks, one solution we can use is *fencing tokens*. We keep an incrementing write token whenever a write lock is granted, If your write lock is not the most recent incremented number, then it is rejected. See the below image.

![](../Attachments/Pasted%20image%2020230127160220.png)


## Byzantine Faults

What if one of the nodes in your system is actively malicious and is trying its best to lie and fool the system? Such behavior is known as a *Byzantine Fault*, and the problem of reaching consensus in this untrusting environment is known as the *Byzantine Generals Problem*. A system is *Byzantine-Fault Tolerant* if it continues to operate correctly even if some of the nodes are malfunctioning and not obeying the protocol, or if malicious attackers are interfering with the network

This rarely comes up in regular distributed systems. It is a concern in spacecraft due to radiation damage to the electronics. But we can safely assume all of our nodes in our datacenter are not acting maliciously.

The major use case and solution to this problem is peer-to-peer networks like Bitcoin and other blockchains. They are a way to get mutually untrusting parties to agree whether a  transaction happened or not, without relying on a central authority.