

A load balancer is a type of [Reverse Proxy](../Internet%20&%20Networking/Proxy.md) that distributes incoming traffic among different nodes. This is done to lower the load on any individual machine, and is key to scaling a web service. Load balancers can operate on one of two layers of the [OSI Model](../Internet%20&%20Networking/OSI%20Model.md).

![](../Attachments/Pasted%20image%2020220417165133.png)

## Load Balancing Algorithms

There are many strategies to decide how to distribute load among the servers, here are some of the more popular ones:
1. Round Robin: The simplest and most commonly used algorithm. Client requests are simply distributed to application servers is simple rotation. This is most appropriate for predictable client request streams that are spread out among servers with equal processing capabilities.
2. Weighted Round Robin: This is similar to round robin, with a key change to adapt to servers with heterogeneous capabilities. Client requests are distributed according to the relative capacity of each server. For example, if server 1 is twice as strong as server 2, then we assign server 1 with double the weight, and thus double the client requests.
3. Least Connections: This is a dynamic load balancing algorithm where client requests are distributed to the servers with the least number of active [TCP](../Internet%20&%20Networking/TCP.md) connections at the time the client request is received. Sometimes, one server may be overloaded due to longer lived connections; this algorithm seeks to take active load into consideration.
4. [Consistent Hashing](Consistent%20Hashing.md): This algorithm can be used to load balance and always send users to the same servers. Coupled with [Caches](../Databases/Database%20Cache.md) this improves performance.


## Layer 4 Load Balancer

With a transport layer load balancer, only the [IP](../Internet%20&%20Networking/IP.md) address and port of the request are looked at.

Pros
- Simpler load balancing
- Efficient (no data lookup)
- More secure (does not look at packet data)
- One [TCP](../Internet%20&%20Networking/TCP.md) connection

Cons
- No smart load balancing
- Not applicable for microservices
- Sticky Segments (segments must go to the same server)
- No Caching


## Layer 7 Load Balancer

With an application layer load balancer, the data is accessed, and decrypted if the data is encrypted. For instance, if the path of the request is for /pictures the load balancer can send the traffic to the specific pictures server. It can also add headers and any other content as it has complete access to the request data. This application layer load balancing cannot be done with a layer 4 balancer.

Pros
- Smart load balancing
- Caching
- Microservices

Cons
- Slightly more Expensive (looks at data)
- Decrypts (terminates [TLS](../Internet%20&%20Networking/Security/TLS.md))
- Two TCP Connections
- Must share TLS certificate