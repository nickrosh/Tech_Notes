
A *Proxy Server* is a server that acts as an intermediary between a client requesting a resource and the server making the resource.

![](../Attachments/Pasted%20image%2020230125004242.png)

This is actually similar to [NAT translation](IP.md), except Proxies are applications that operate on [layer 7](OSI%20Model.md) while NAT is on the layer 3 protocol level. Proxies can be done to remain anonymous, or to evade content blockers. A proxy that passes unmodified [HTTP](HTTP.md) requests is usually called a *gateway*.


## Reverse Proxy

*Reverse Proxies* are the same idea, except that the proxy is used by the server, not the client.

![](../Attachments/Pasted%20image%2020230125005712.png)

The clients are sent to the publicly facing proxy server. This can be done for a number of reasons:
- The proxy can act as a [Load Balancer](../Distributed%20Systems/Load%20Balancer.md) to distribute requests among servers
- The proxy can be configured to perform [TLS](Security/TLS.md) acceleration, to encrypt/decrypt SSL faster than can be done by the web server itself.
- To optimize and compress the content to speed up the load time
- The server can be another layer of security before pushing the content to the servers