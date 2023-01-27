
The *Border Gateway Protocol* is a standardized protocol to exchange routing and information among *autonomous systems* like ISPs and large corporations (Comcast, Facebook, etc.) on the [Internet](TCP-IP%20Model.md).

![](../Attachments/Pasted%20image%2020230125002258.png)

This web of interconnections is how we have a world wide internet. There are distributed systems that make Peering agreements to link their networks. To allow every system to see every other system, one could create a peer connection between every single system. This full mesh system would be extremely costly though. The solution is *Route Reflectors*, which is a router that connects with multiple systems, and is connected to other Route Reflectors. Routes are always reflected to BGP peers, but not to the originator of the route. This concentrates the connections and reduces the number of connections needed for all systems on the internet to see each other.

![](../Attachments/Pasted%20image%2020230125003651.png)