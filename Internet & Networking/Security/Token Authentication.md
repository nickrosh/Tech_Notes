

Token Authentication is a stateless authentication method that does not require [Cookies](Cookies.md).

![](Pasted%20image%2020220831010621.png)

The user sends a request. If they are validated, then the server sends back a token. The user can then use that token to access requested pages without needing to be validated against a session ID.

### JSON Web Token

JWTs are a popular type of token authentication for [REST API](REST%20API.md)s.

![](Pasted%20image%2020220831010824.png)

The JWT is a 3 part token. Note that the token is not encrypted so don't send secrets in the payload.

1. Header: the algorithm and token type
2. Payload: the data
3. Signature: determines the validity of the token

The secret Key and the payload go through a hashing algorithm to create the signature. Hackers can edit the token, but without the secret key, they can't make a valid signature.


### Pros/Cons

Tokens can be useful when the user wants to reduce the number of times they must send their credentials. In the case of server-to-server connections, using credentials becomes difficult, and tokens overcome this problem. Moreover, servers that use tokens can improve their performances, because they do not need to continuously look through all the session details to authorize the user’s requests. 

However, the authentication details are stored on the client, so the server cannot perform certain security operations as in the session method. As written above, the server does not authenticate the user, so linking a token to its user can be more difficult. If a hypothetical attacker manages to get a valid token, they may have unlimited access to the server databases. If the server generates keys using older algorithms, these keys can be breached.