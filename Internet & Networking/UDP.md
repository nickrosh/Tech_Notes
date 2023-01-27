
The *User Datagram Protocol* is a core [internet](IP.md) communication protocol like [TCP](TCP.md). It operates as a transport layer in the [TCP-IP Model](TCP-IP%20Model.md) or Layer 4 in the [OSI Model](OSI%20Model.md). UDP uses a simple connectionless communication model with a minimum of protocol mechanisms. It has no handshaking dialogue, and there is no guarantee of delivery, ordering, or duplicate protection. If and application needs data integrity certainty, they can use [TCP](TCP.md).

UDP is useful when error checking and correction are not necessary, or are too costly. For example in *Voice over IP* which is a live video feed, you would much rather have a clean video than a choppy one coming from the TCP checks. If a few frames are lost in the video, it matters much less than continuing live data. The UDP header follows this simplicity, it is just 8 bytes:

![](../Attachments/Pasted%20image%2020230125010828.png)

Also, being connectionless means you can use UDP to *broadcast*. This means sent packets can be addressed to be receivable by all devices on the subnet.