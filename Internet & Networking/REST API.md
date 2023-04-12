

REST (Representational State Transfer) defines a architectural style of API. It defines using [HTTP](HTTP.md) methods and well defined path names of API endpoints to define operations between a client and a server. These operations break down into the fundamental *Create, Read, Update, Delete*, also known as CRUD. These methods also line up with *INSERT, SELECT, UPDATE, DELETE* in [Relational Databases](../Databases/Relational%20Databases.md).

![](../Attachments/Pasted%20image%2020220413235626.png)

## API Design

When designing an API, you disregard  the implement details of the API itself. We are only concerned with the surface area that is exposed to the user. With publicly facing APIs, you cannot easily change the API as it would break all integrations connected to your API. Hence this is why APIs are versioned like `api.website.com/v1/post` with v1, v2, etc. being the version of the API. This backwards compatibility and inflexibility of design dictates that we must design API's very carefully with future usage also kept in mind. For this reason, we also use many optional parameters, so that we can add additional features to the response without necessitating a new API version.

#### Specific Objects

When we want to POST a new object or GET all objects, we would simply make the URL a general name like `/tasks`. However, if we want to only GET a specific object, or update or delete an object, we would use a URL path parameter: `/tasks/:id`, with `:id` being a variable of whatever the user ID is. This parameter can be whatever is desired, and is usually the way API's allow specific object requests.

#### Pagination

If the user does want to GET every object, this may involve a huge amount of data. In order to not overwhelm the server and database, we utilize *pagination* in the API which limits the number of objects in one request. This is done with two query parameters, *limit* which states how many objects will be returned in a single request, and *offset* which states what offset in the table to get the data. So `/tasks?limit=10&offset=0` would return the first 10 objects in `/tasks`. `/tasks?limit=10&offset=10` would return objects 10-20 to the user. It is better to have these parameters as optional, with offset defaulting to 0 and limit defaulting to some small amount.

Many times, it is common to use a pagination token instead of an offset parameter. This would be returned in the first request and would keep track of how many objects have already been seen and would flip through the "pages" for you.


## REST Architectural Constraints

A true REST API is more than just a [HTTP](HTTP.md) API, however these days most people believe they are one and the same. The REST paper described constraints of a truly REST API, with Statelessness and Cacheability being chief among them:

1. **Client-Server Architecture**: There is a separation of concerns, where it is the client requesting resources from the server. This tightly separates concerns, improves scalability, and allows components to evolve independently.
2. **Statelessness**: Requests are stateless, and no session information is retained by the receiver. Relevant session data is sent to the receiver by the client in a way that every packet of information transferred can be understood in isolation, without context from previous requests. This property allows services to be ideal for high-volume applications, by not being weighed down by retention of session information.
3. **Cacheability**: You can cache responses. Responses must, implicitly or explicitly, define themselves as either cacheable or non-cacheable to prevent clients from providing stale or inappropriate data in response to further requests. Well-managed caching improves scalability and performance by reducing the number of client-server interaction needed
4. **Layered System**: Layered web services can be assisted by [Load Balancing](../Distributed%20Systems/Load%20Balancer.md), [TLS](Security/TLS.md) termination, and will help scalability
5. **Uniform Interface**: There are four components
	1. Resource Identification in Requests: like [JSON, XML, HTML](Serialization.md), etc.
	2. Resource Manipulations through representations: when a client holds a representation of a resource, including any metadata attached, it has enough information to modify or delete the resource's state.
	3. Self-descriptive Messages: each message includes enough information to describe how to process the message.
	4. Hypermedia as the Engine of Application State: a client should be able to find all the resources they need from the initial URL for the REST app.