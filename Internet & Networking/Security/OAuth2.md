
*OAuth* short for Open Authorization, is an open standard for access delegation. Commonly used as a way for internet users to grant websites or apps access to their information on other websites but without giving them the passwords. This is how you can access the resources of one service through another application entirely. This is similar to [OpenID](OpenID.md) which is for authentication.


![](../../Attachments/Pasted%20image%2020230125020959.png)

*OAuth2* is the modern version, and comes with a pretty complex system to verify a user:
1. Make an Authorization request to the Application
2. The App will then take your authorization request and route it to whatever Authorization service you want to use, let's say Google. You will be redirected to Google login if you do not already have an active session open.
3. If you are authorized, the Authorization server will return back [Tokens](Token%20Authentication.md) that will authorize later requests to Google
	- `access_token`
	- `id_token`
	- `refresh_token`
4. Now the application will make [HTTP](../HTTP.md) requests to Google using the tokens you've been given. This allows the app to use your Google data without ever having access to your password.

Note that you have to refresh your tokens in OAuth.