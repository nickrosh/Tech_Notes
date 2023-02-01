
CAP is presented as *Consistency, Availability, Partition Tolerance*. Although it sometimes presented as *pick 2 out of the 3*, partition tolerance is a [network fault](Distribution%20Problems.md), you don't choose it.

![](../Attachments/Pasted%20image%2020230129190710.png)

At times when the network is working correctly, a system can provide both consistency ([linearizability](Consistency%20and%20Consensus.md)) and total availability. When a network fault occurs, you have to choose between either linearizability or total availability. Thus a better way of phrasing CAP is *In the presence of a Network Partition (network fault), choose consistency or availability*.

The CAP Theorem as formally defined is of very narrow scope: it only considers one consistency model (namely linearizability) and one kind of fault (*Network Partition* aka nodes that are alive but disconnected from each other). It doesn't say anything about network delays, dead nodes, or other trade-offs.