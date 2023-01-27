

*Transmission Control Protocol* or TCP is the backbone of the modern internet and a key component of the [TCP-IP Model](TCP-IP%20Model.md). It ensures that all data from [IP](IP.md) packets are correctly transferred between a source and destination. IP packets can and do drop all the time as they are going through physical wires over thousands of miles long, so it became critical to ensure that there is no data losses when sending files and documents over the internet.

### 3-way handshake

![](../Attachments/Pasted%20image%2020220416151554.png)

TCP performs a "3-way-handshake" when it establishes a connection to ensure the server is active and connected. 

1. **SYN**: The Client sends a **SYN** (synchronize) random number to the server
2. **SYN-ACK**: The Server responds by sending its own **SYN** and also adding a **ACK** (acknowledge) which is the client's **SYN + 1** to demonstrate the server received the packet
3. **ACK**: The Client replies by sending its own **ACK** which is just the servers **SYN + 1**, to demonstrate the client is also active.

When a packet is lost in transit, the receiver will be aware because the continuous numbering of the packets means that it is obvious when one is missing. The client will then request that missing packet from the server. This ensures there is no lost data when using TCP. The TCP header added to every packet is the following:

![](../Attachments/Pasted%20image%2020230124233133.png)

