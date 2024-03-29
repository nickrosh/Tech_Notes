

Transport Layer Security is a security protocol in the [Application Layer](../TCP-IP%20Model.md) that encrypts and decrypts data sent over the internet. It is responsible for encrypting [HTTP](../HTTP.md) into the secure form HTTPS. TLS has the server send over its credentials from a **Certificate Authority (CA)** as a source of truth of a server's identity. Then, cryptographic session keys are created and exchanged which allow the client and server to have a secure encrypted channel.


![](../../Attachments/Pasted%20image%2020220416160434.png)

1. The handshake begins when a client connects to a TLS-enabled server requesting a secure connection and the client presents a list of supported cipher suites (ciphers and [hash functions](../../Data%20Structures%20&%20Algorithms/Data%20Structures/Hash%20Maps.md))
2. From this list, the server picks a cipher and hash function that it also supports and notifies the client of the decision.
3. The server usually then provides identification in the form of a *digital certificate*. The certificate contains the server name, the trusted *certificate authority* (CA) that vouches for the authenticity of the certificate, and the server's public encryption key.
4. The client confirms the validity of the certificate before proceeding.
5. To generate the session keys used for the secure connection, the client either:
    -   encrypts a random number (_PreMasterSecret_) with the server's public key and sends the result to the server (which only the server should be able to decrypt with its private key); both parties then use the random number to generate a unique session key for subsequent encryption and decryption of data during the session
    -   uses *Diffie-Hellman Key Exchange* to securely generate a random and unique session key for encryption and decryption that has the additional property of forward secrecy: if the server's private key is disclosed in future, it cannot be used to decrypt the current session, even if the session is intercepted and recorded by a third party.