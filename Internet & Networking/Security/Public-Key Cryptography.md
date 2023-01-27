
*Public-Key cryptography* or asymmetric cryptography uses pairs of related keys. Each pair consists of a *public key* and a *private key*. It is asymmetric as public keys can be safely distributed publicly. This is how [TLS](TLS.md) and SSL encryption is done.

## Public-Key Encryption

![](../../Attachments/Pasted%20image%2020230125015231.png)

In a *Public-Key Encryption* system, anyone with a *Public Key* can encrypt a message, yielding a *ciphertext* but only those who know the corresponding *private key* can decrypt the ciphertext to obtain the original message. An eavesdropper cannot read the message as they cannot decipher the ciphertext. However, the computer metadata will still be unencrypted with the message


## Digital Signatures

![](../../Attachments/Pasted%20image%2020230125015118.png)

In a *Digital Signature* system, a sender can use a private key together with a message to create a *signature*. Anyone with the corresponding public key can verify whether the signature matches the message, but a forger who does not know the private key cannot find any message/signature pair that will pass verification with the public key.

Note that you would not use the same type of key for these two use cases. They are different and require different cryptographic algorithms.