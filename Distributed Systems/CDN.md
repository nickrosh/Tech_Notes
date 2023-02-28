
A *Content Delivery Network* (CDN) is a distributed static content delivery server that responds to user [HTTP](../Internet%20&%20Networking/HTTP.md) requests much faster than a single server. 

![](../Attachments/Pasted%20image%2020230208001511.png)

There is a single *Origin Server* that acts as the actual server for the website. The CDN servers are like a [Cache](../Databases/Database%20Cache.md) that stores and serves static files like HTML, CSS, JavaScript, Images, and Videos. This means a user in a country very far from the origin server can still receive their static files with low latency

Images and Videos do not generally change too much in an application, which is why it is much easier to just store it on CDN servers, to both decrease the latency, reduce load on the origin, and provide greater availability in case of a [network fault](Distribution%20Problems.md).


#### Push CDN

In a push configuration, all data stored on the origin server is immediately pushed to every single CDN. This is analogous to a *write-around cache*.


#### Pull CDN

In a pull configuration, data on the origin is not automatically sent to the CDNs. Instead, when a user makes a request to a CDN, and it is a [Cache Miss](../Databases/Database%20Cache.md), the CDN itself will grab the data from the origin server. This means that there will initially be more cache misses, but the CDNs will have data much more relevant to local users. If a certain region only wants data in one language, it doesn't make sense to push that content to every CDN. This is analogous to a *write-through cache*.

## Edge Servers

A new type of CDN actually allows you to execute code on distributed servers, combining the quick response times of CDNs as well as the flexibility and [REST API](../Internet%20&%20Networking/REST%20API.md) capabilities of a regular backend server.