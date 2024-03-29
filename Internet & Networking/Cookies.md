
Cookies allow you to send session data and metadata with each request. They can be made from the client or the server. They can also be made by 3rd parties for tracking purposes. Cookies are sent with every [HTTP](HTTP.md) request as a header.

### Creating Cookies
Two methods to create cookies:
1. **Client JavaScript** - Document.cookie
2. **Web Server**- Set-Cookie [header](HTTP.md)


### Cookie Properties
- **Cookies are sent with every request**
- **Cookie Scope:**
	- Domain: cookies are restricted a single domain by default
	- Path: you can set cookies to a specific path with the `path` argument. You want to make sure your cookies are properly scoped so you don't send unnecessary data with every request.
- **Expires, Max Age:** If you don't specify these properties, the cookies will be destroyed when the browser is closed. These are called _Session Cookies_.
- **Same Site:** This sets the policy of when to send cookies, to protect against Cross Site Request Forgery and other attacks. By default, cookies are not sent on normal cross-site sub-requests (for example to load images into a third party site), but are sent when a user is _navigating_ to the origin site, i.e. following a link.


### Cookie Types
- **Session Cookies:** Expire when the current session/browser ends, this is the default.
- **Permanent Cookies:** Do not expire when the session ends. They may end in 10 minutes, or 1000 years if the server wants it around truly permanently.
- **HTTPonly Cookies:** Can only be read from the server, and can't be read by JavaScript. This is for security purposes to prevent cross-site forgery, and useful for session IDs or tokens.
- **Secure Cookies:** Can only be sent to HTTPS domains, so it will only work with [TLS](Security/TLS.md) encryption
- **Third Party Cookies:** Cookies coming from other sources, they cannot read your cookies, but they can track you as they see your requests.
- **Zombie Cookies:** cookies that can be regenerated by code whenever they are cleared by the browser. This is a breach of security and trust.


### Cookie Security
- **Stealing Cookies:** stealing cookies and using the info
- **Cross Site Request Forgery:** stealing a cookie and posing as the user