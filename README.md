# TLS HANDSHAKE
This will provide insight on how the TLS handshake works and how we can analyze the steps of the handshakes with Wireshark.

## TLS Introduction
Transport Layer Security (TLS for short) is an upgraded version of the Secure Socket Layer protocol (SSL for short). It is  cryptographic protocol that provides security in inetrnet communications.

When a client requests for a webpage, for example, they are sending an HTTP message. This payload is encapsulated in a TCP packet, which is the encapsulated in an IP packet and in the end an Ethernet frame. To put it simply; headers are being added to the HTTP message:
++ add picture
TLS is placed between the **application layer** (_which uses HTTP_) and the **transport layer** (_wich uses TCP_).
++add picture

TLS uses both symmetric and asymmetric encryption for the following purposes:
- Asymmetric for server authentication; _**authentication**_
- Symmetric for data encryption; _**privacy & integrity**_

This will be analyzed in furhter detail later, but it is important to mention that both the client and the server create a _Message Authentication Code_ (MAC for short) to make sure that no malicious party has intercepted their communication.

## TLS Handshake
If we open up a webpage in a browser and click on the padlock icon next to the URL w can see that we have a TLS session. This means our browser and the server that hosts that specific webpage have established a TLS connectio, over TCP, which encrypts HTTP before it is delivered and received.
++add picture

If we take a look into the details we can actually see the **cipher suite**.
_What is a **cipher suite**?_ It shows us how the internet communication works. Cipher suites don't just ensure security, but also the compatibility and performance of HTTPS connections.

#### Cipher Siute Explanation
This is a brief explanation of the cipher suite above:
- **ECDHE**: Elliptic Curve Diffie Hellman Ephemeral will be used for the key exchange
- **RSA**: the asymmetric algorithm that will be used for the authentication of the server's certificate
- **AES**: the symmetric algorithm that will be used for the encrypted communication 
- **128**: the length of the keys
- **GCM**: Galois/Counter Mode
- **SHA256**: the hash function used


## TLS Traffic Analysis with Wireshark 
These are the packets that were captured when I requesed my university's webpage.
> The **'tls'** filter was used.

++add picture

#### Client Hello
Client sends a message which contains, along with other things:
- What is the max TLS version the client supports
- A random number (to avoid replay attacks)
- A list of cipher suites (in this case 16)
- The server's name (this is not always possible since TLS 1.3 doesn't support this)
- Statement of intent to use a Certificate Status Protocol to authenticae the server (in this case OCSP)
- A list of cryptographic parameters for the key exchange with ECDHE or DHE
- A PSK from a previous, safe communication

#### Server Hello
The server replies by sending back to the client:
- The TLS version they can both support
- A random nuber (to avoid replay attacks)
- One of the cipher suites from those the client has sent

#### Certificate – Server Key Exchange – Server Hello Done
The server sends its certificate, which the client checks, and a **Server Key Exchange** which contains:
- The parameters that will be used for the key exchange
- The server's _**digital signature**_, which will be used for the server's authentication; since it is created with the server's private key

