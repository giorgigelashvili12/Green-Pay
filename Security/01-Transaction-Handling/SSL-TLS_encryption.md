# What is SSL/TLS Encryption
## SSL/TLS encrypts communication between a client and a server, primarily web browsers and web sites/application.
SSL (Secure Socket Layer) encryption, and its more modern and secure replacement, TLS (Transport Layer Security) encryption, protect data sent over the internet or a computer network. This prevents attackers from viewing oe tampreing with data exchanged between two nodes: User's web browser and a web server. Most website owners and operators have an obligation to implement SSL/TLS to protect sensetive data exchanging such as personal infromation considered private.

## How Does SSL/TLS Encryption Work
SSL/TLS uses both: asymmetric and symmetric encryption to protect the confidentiality and integrity of data tansition. Asymmetric encryption is used to stablish a secure session between a client and a server, and symmetric encryption is used to exchange data within the secured session.
For this implementation a website must have an SSL/TLS certificate for their web domain. The certificate enables the client and server to securely negotiate the level of encryption:
1. The client contacts the server using a secure URL
2. The server sends the client its certificate and public key
3. The client verifies this with a Trusted Root Certification Authority to ensure the certificate is legitimate
4. The client and server negotiate the strongest type of encryption
5. The client encrypts a session key with the server's public key, and sends it back to the server
6. The server decrypts the client communication with its private key, and the session is established
7. The session key (symmetric encryption) is now used to encrypt and decrypt data transmitted between the client and the server.

Both the client and the server are now using HTTPS for their communication. Once you leave the website those keys are discarded, meaning that on your next visit a new set of keys are generated

## Why is SSL/TLS Decryption Important for Security?
SSL/TLS encryption is great for security because it increases confidentiality and integrity of data communication. Attackers also use enccryption to hide malicious payloads, effective decryptions is necessary for inspection tools.