


REST (Representational State Transfer) defines a architectural style of API. It defines using [HTTP](HTTP.md) methods and well defined path names of API endpoints to define operations between a client and a server. These operations break down into the fundamental *Create, Read, Update, Delete*, also known as CRUD. These methods also line up with *INSERT, SELECT, UPDATE, DELETE* in [Relational Databases](../Databases/Relational%20Databases.md).

![](../Attachments/Pasted%20image%2020220413235626.png)

### REST Architectural Constraints

1. **Client-Server Architecture**: There is a separation of concerns, where it is the client requesting resources from the server. This tightly separates concerns, improves scalability, and allows components to evolve independently.
2. **Statelessness**: Requests are stateless, and no session information is retained by the receiver. Relevant session data is sent to the receiver by the client in a way that every packet of information transferred can be understood in isolation, without context from previous requests. This property allows services to be ideal for high-volume applications, by not being weighed down by retention of session information.
3. **Cacheability**: You can cache responses. Responses must, implicitly or explicitly, define themselves as either cacheable or non-cacheable to prevent clients from providing stale or inappropriate data in response to further requests. Well-managed caching improves scalability and performance by reducing the number of client-server interaction needed
4. **Layered System**: Layered web services can be assisted by load balancing, TLS termination, and will help scalability
5. **Uniform Interface**: There are four components
	1. Resource Identification in Requests: like JSON, XML, HTML, etc.
	2. Resource Manipulations through representations: when a client holds a representation of a resource, including any metadata attached, it has enough information to modify or delete the resource's state.
	3. Self-descriptive Messages: each message includes enough information to describe how to process the message.
	4. Hypermedia as the Engine of Application State: a client should be able to find all the resources they need from the initial URL for the REST app.