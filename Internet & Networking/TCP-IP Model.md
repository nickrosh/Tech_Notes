

The TCP/IP represents the network model of the modern internet. It is specifically made for the Internet Suite of protocols, unlike the [OSI Model](OSI%20Model.md).

![](../Attachments/Pasted%20image%2020220416152501.png)

### Application
This is the application such as SSH, FTP, or [HTTP](HTTP.md). Each application has its own designated Port, like SSH on 22 or HTTP on 80. This layer also contains the security protocols [TLS](Security/TLS.md) and **SSL** which concern the encryption and decryption of data, and the ability to use **HTTPS**.


### Transport
This is the transport protocol. This is usually either [TCP](TCP.md) to ensure data completeness or **UDP** for speed. 


### Internet
This is the **Internet Protocol** (IP) layer which splits the data into IP Packets which contain the source and destination addresses


### Link
This is the lowest level, concerning breaking the data into frames so they can be sent over the wire in data transmission formats like **Ethernet**.