## Level 15 to 16

Goal: retrieve the password for level 16 by submitting the current level's password to port `30001` on the local host using SSL/TLS encryption.

What was done:

`openssl s_client -connect localhost:30001` -> established an SSL/TLS connection to the service running on port `30001`

`<bandit15 password>` -> submitted the current password through the encrypted connection and received the password for level 16

After logging in as `bandit15`, the goal stated that the current password had to be submitted to port `30001` on the local host using SSL/TLS.

`openssl` had already been researched based on the recommended commands for previous levels, so the required tool was already understood. The connection was therefore established directly with:

`openssl s_client -connect localhost:30001`

The `-connect` option was used to specify the host and port that `openssl s_client` should connect to. In this case, the target was `localhost` on port `30001`.

When the connection was established, `openssl` displayed a large amount of information related to the TLS connection, including the certificate chain, server verification, cipher suite negotiation, session tickets, and verification messages.

This output initially appeared confusing because it was not directly related to the password itself. However, it represented the automatic TLS handshake and connection information produced by `openssl s_client`. The information did not need to be manually interacted with for the purpose of the level.

The current password was then entered into the established connection. The service accepted it and returned the password for level 16.

What was understood:
- `openssl s_client` can be used to establish an SSL/TLS connection to a network service.
- `-connect` specifies the destination host and port for the connection.
- SSL/TLS automatically performs a handshake before application data is exchanged.
- The certificate chain, cipher suite, session information, and verification messages are part of the TLS connection process.
- The technical output produced during a TLS handshake does not necessarily need to be interacted with when the objective is simply to communicate with the service.
- The correct tool can make a protocol-specific network interaction straightforward when the underlying protocol is already understood.
- A service listening on a port may require encrypted communication even when the interaction itself is as simple as submitting a password.

Vulnerability type: this level demonstrates protocol-specific network communication using SSL/TLS. It also demonstrates how services can require encrypted transport rather than accepting plaintext communication.

Real world version: SSL/TLS is used to protect data exchanged between clients and services, including authentication credentials and other sensitive information. Security testing may require identifying whether a service expects plaintext or encrypted communication and using the appropriate protocol when interacting with it.

Cloud version: SSL/TLS is widely used by cloud services to protect data in transit between clients, APIs, load balancers, and backend services. Understanding how TLS connections are established and verified is therefore important when securing and troubleshooting cloud-based services.