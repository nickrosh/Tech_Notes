

**Hyper Text Transfer Protocol** is an application protocol of the internet running on port 80. It defines a simple client-server protocol of sending messages as requests and receiving responses based on the request parameters. 

### HTTP Requests

![](Pasted%20image%2020220414001657.png)

A request contains the following components:

- an HTTP Method, usually a verb such as `GET` or `POST`, or even less used nouns like `OPTIONS` or `HEAD`, denoting the action the client wants to perform. Typically, a client wants to fetch a resource (using `GET`) or post the value of an HTML Form (using `POST`), though more operations may be needed in other cases. 
- The path of the resource to fetch, the URL of the resource stripped of elements obvious from the context: protocol, domain, or the port.
- The version of the HTTP Protocol
- Optional Headers that convey additional information from the servers.
- A Body, for some methods like `POST`, similar to those in responses, which contain the resource sent.


### HTTP Responses

![](Pasted%20image%2020220414004124.png)

A response contains the following elements:

- The version of the HTTP protocol they follow
- A status code, indicating if the request was successful or not, and why.
- A status message, a non-authoritative short description of the status code
- HTTP Headers, like those for requests
- Optionally, a body containing the fetched resource.


### Methods

`GET` => requesting a resource
`HEAD` => asks for a response identical to `GET` expect without the body
`POST` => submits an entity to the specified resource, often causing change in state
`PUT` => replaces all current representations of the target resource with the request payload
`DELETE` => delete the specified resource
`CONNECT` => establish a tunnel to the server identified by the target resource
`OPTIONS` => describe the communications options for the target resource
`TRACE` => performs a message loop-back test along the path to the target resource.
`PATCH` => applies partial modifications to the resource


### Headers
Here are the 5 most popular headers, but there are many many more.

`Authorization` => This client-side header communicates the authorization status of the user. This is a token with the authorization status of the user. It is different from authentication, which is concerned with the actual validation of the user's identity. The authorization header is generally used to send a [Token](Token%20Authentication.md) that the server can process to determine if the user is allowed to access the given resource.

`Content-Type` => this client and server-side header tells the recipient of the request what the body of the request contains. There are many options but some of the most common are:
- `application/json` => for JSON API data
- `text/html` => for HTML to render the site
- `text/css` => for the styling

`Accept` => this client-side header determines the data type that your application is expected to be able to understand. Useful to prevent a data mismatch from the server.

`Cookie` => this header contains other metadata stored in [Cookies](Cookies.md) on the users browser. These are often, but not exclusively, originally set by the server with the `Set-Cookie` header, and can be used to communicate any type of metadata about the user, including browsing history or login status.

`Access-Control-Allow-Origin` => This is a server-side header that provides information about the servers accepted web addresses, as per **Cross Origin Request Sharing (CORS)**. 


### HTTPS
HTTP is an unencrypted protocol, and thus the data can be seen by hackers. [TLS](TLS.md) provides encryption and security to HTTP in the form of a protocol called **HTTP-Secure** or **HTTPS**. This new protocol running on port 443 is now the de-facto standard. 