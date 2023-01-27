
The *Internet Protocol* is the [network layer](OSI%20Model.md) communications protocol of the [TCP-IP](TCP-IP%20Model.md) internet suite. Each IP packet has two components: a header and a payload. The IP header includes the source IP address, destination IP address, and other metadata needed to route and deliver the datagram.

## IPv4

IP Version 4 is the initial and currently most popular format for IP packets. The format of the IPv4 is shown below:

![](../Attachments/Pasted%20image%2020230124214041.png)

The most important thing to know about IPv4 is that the IP address lengths are 32 [bits](../Electrical%20Engineering/Digital/Binary.md). This means there are only a few billion unique addresses that can be used. In the IPv4 header, the source and destination 32-bit addresses are labelled, as well as the length of the header, as well as "Time to Live". This field limits the packet's lifetime to prevent network failure in the event of a routing loop (infinite redirection loop). This field is specified in seconds, but anything less than one is rounded to one. In reality, this field is used as a "hop count": when the packet arrives at a router, the router decrements the TTL field by one. When the field hits zero, the router discards the packet and sends back a "time exceeded" message.

IPv4 addresses are written in four decimal numbers representing the four bytes of the address: 

![](../Attachments/Pasted%20image%2020230124215339.png)

#### Subnetting

When a router wants to route a message to the right destination, it accesses its *routing table* which lists the routes to particular network destinations. The number of routes a router needs to store can become huge, especially as more and more addresses are used across the entire address space. To alleviate this, *subnets* and *CIDR* were introduced.

IP addresses are described as consisting of two groups of bits, the most significant bits are the *network prefix* which describes a whole network or subnet. The least significant bits are the *host identifier*, which specifies a particular interface of a host on that network. This division is used as the basis of traffic routing between IP networks and for address allocation policies. We use *subnet masking*:

![](../Attachments/Pasted%20image%2020230124230558.png)

This is a system where we use [bitwise AND masking](../Software%20Engineering/Bitwise%20Operations.md) to determine the network portion. If we want the first three bytes, or 24 bits, to be used to denote the network, that would have a subnet mask of `255.255.255.0`. If we wanted to create subnets to alleviate network bandwidth, we could increase this mask to another few bits, so like `255.255.255.128` which has 25 ones. We can easily display a subnet with CIDR notation which is just the IP address, followed by a slash and the number of bits being masked, so the above IP address would be denoted as `192.168.100.1/24`.

#### NAT

*Network Address Translation* is another necessity coming from the limited number of IPv4 addresses. Local Networks will change the address of local hosts. This means that the router will have the "public" address and all the local devices will have "private" addresses, so an entire only needs one IPv4 address to work. Whenever a request is made outside the network, the public IP will be used.

![](../Attachments/Pasted%20image%2020230125005241.png)


## IPv6

![](../Attachments/Pasted%20image%2020230124214240.png)


Even with subnets and CIDR, we are still running out of IPv4 addresses. IPv6 solves the issue of a limited IP Address space by making IP addresses 128-bits long, thus allowing up to $2^{128}$  . Additionally, many of the unused parts of the IPv4 header are removed, so the IPv6 header is streamlined. IPv6 removes the need for NAT, every single device on the planet can have a unique identifier. This is an attempt to return to the original intent of the internet as point-to-point communication.