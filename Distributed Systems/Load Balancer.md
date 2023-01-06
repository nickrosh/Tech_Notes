

A load balancer is a **Reverse Proxy** that distributes incoming traffic among different nodes. This is done to lower the load on any individual machine, and is key to scaling a web service. Load balancers can operate on one of two layers of the  [OSI Model](OSI%20Model.md).

![](Pasted%20image%2020220417165133.png)


### Layer 4 Load Balancer
With a transport layer load balancer, only the IP address and port of the request are looked at. You can distribute load depending on the IP address, like round robin

Pros
- Simpler load balancing
- Efficient (no data lookup)
- More secure (does not look at packet data)
- One [TCP](TCP.md) connection

Cons
- No smart load balancing
- Not applicable for microservices
- Sticky Segments (segments must go to the same server)
- No Caching


### Layer 7 Load Balancer
With an application layer load balancer, the data is accessed, and decrypted if the data is encrypted. For instance, if the path of the request is for /pictures the load balancer can send the traffic to the specific pictures server. It can also add headers and any other content as it has complete access to the request data.

Pros
- Smart load balancing
- Caching
- Microservices

Cons
- Slightly more Expensive (looks at data)
- Decrypts (terminates [TLS](TLS.md))
- Two TCP Connections
- Must share TLS certificate