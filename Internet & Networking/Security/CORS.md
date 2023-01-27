
*Cross-Origin Resource Sharing* is a mechanism that allows restricted resources on a web page to be [requested](../HTTP.md) from another [domain](../DNS.md) outside the domain from which the first resource was served. Cross domain requests are prevented by default to prevent *Cross-Site Scripting* which is when attacker inject client-side scripts into web pages viewed by other users.

![](../../Attachments/Pasted%20image%2020230125013224.png)


For [HTTP](../HTTP.md) methods that can modify data, CORS mandates that browsers "preflight" the request, first sending an OPTIONS request. Once the server "approves" the preflight, then the real request can be sent. Servers can also notify clients whether "credentials" (including [Cookies](../Cookies.md) and [Token Authentication](Token%20Authentication.md)) should be sent with requests.

## Headers

The headers that relate to CORS are:

#### Requests:
- `Origin`
- `Access-Control-Request-Method`
- `Access-Control-Request-Headers`

The preflight request contains these headers with data about the request

#### Response:
- `Access-Control-Allow-Origin`
- `Access-Control-Allow-Credentials`
- `Access-Control-Allow-Methods`
- `Access-Control-Allow-Headers`
- `Access-Control-Expose-Headers`
- `Access-Control-Max-Age`

If the preflight request headers match these headers, then the request will be allowed. The server sets these CORS policies. To allow any origin, set the value to wildcard (`*`)