
OpenID is a decentralized authentication protocol. It allows users to be authenticated by co-operating sites using a third provider service. This is the protocol that allows you to login to a random website with your Google account. OpenID is similar to [OAuth2](OAuth2.md) which handles third party resource authorization. In fact, the modern OpenID Connect is built on top of Oauth2 to provide a [RESTful](../REST%20API.md) authentication service.

![](../../Attachments/Pasted%20image%2020230125023043.png)


## Difference with Oauth2

OpenID is how to do full authentication, like fully logging into your Google account. Oauth2 is for partial authorization, like getting a key that lets the app access your Google Drive.

![](../../Attachments/Pasted%20image%2020230125022657.png)