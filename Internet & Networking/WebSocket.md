
The *WebSocket* protocol is a bidirectional persistent [TCP](TCP.md) connection between a client and server. This is contrasted with [HTTP](HTTP.md) which is transient and closes the TCP connection when the request has been completed. With WebSockets, the connection is maintained until the application closes it.

![](../Attachments/Pasted%20image%2020230208004504.png)

This allows for immediate state transfer between client and server. A real-time chat application can use WebSocket to make a real time chat. If you attempted to implement the chat with HTTP, you would have to constantly poll the server for changes.