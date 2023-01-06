

With Session Authentication, the DB tracks when a user logs in and when they log off, also takes advantage of [Cookies](Cookies.md). 

![](Pasted%20image%2020220831005557.png)

The server compares the login info against the user information in the DB. If it is correct then the server returns a cookie containing the session ID to the user. The user can then use that cookie to authenticate with subsequent requests

### Pros/Cons
Since sessions are stored on the server, its administrators are in power over them. For example, if they suspect a user is nefarious, they can immediately invalidate their associated Session ID. On the other hand this causes scalability issues as the server must look up the Session ID for every single user

Cookies may be exposed to cross-site request forgery attacks. The attacker may mislead the user to a hostile website, where some JS scripts may exploit cookies to send malicious requests to the server. Another vulnerability regards the chances of a man-in-the middle attack, where an attacker can intercept the session ID and perform harmful requests to the server.