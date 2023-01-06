

OSI is a high-level networking model that tries to define everything from application data to the voltage in the wires. the [TCP-IP Model](TCP-IP%20Model.md) is more popular for internet networking. OSI is a little outdated from the time when there were actually competitors to TCP/IP.

### Layer 7 - Application
This layer is the applications which map to ports, such as SSH on port 22, [HTTP](HTTP.md) on port 80, etc.


### Layer 6 - Presentation
This layer deals with the understanding of incoming data, which may require decoding or decryption. This turns system-dependent representation of the data (like **ASCII**) into an independent form.


### Layer 5 - Session
Session layer provides inter-process communication between two systems, such as **Remote Procedure Call (RPC)**. This is not used much anymore, the connection info is usually taken care of in Layer 4


### Layer 4 - Transport
Transport layer concerns itself with the Transport protocol, such as **TCP** and **UDP**. This is how we ensure we are sending all of the packets needed in the right order and correctness. These packets contain the source and destination port numbers. 


### Layer 3 - Network
This layer is the **Internet Protocol (IP)**, which sends data as packets containing the  source and destination IP addresses.


### Layer 2 - Data Link
Here the packets are broken into frames small enough to send over the wire. Each frame has the source and destination MAC address. Only the most basic bit error checking is done here. Some protocols in this layer include **Ethernet** and **I2C**.


### Layer 1 - Physical
In this layer, data is actually sent over the wire in the form of bits, 0/1 represented by LOW/HIGH voltage with copper, or light with fiber optics.