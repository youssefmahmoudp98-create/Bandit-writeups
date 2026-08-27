## Level 14 to 15

Goal: retrieve the password for level 15 by submitting the current level's password to port `30000` on the local host.

What was done:

`ssh localhost -p 30000` -> attempted to connect to port `30000` using SSH, but the connection was closed

`ssh bandit14@localhost -p 30000` -> attempted another SSH connection while logged in as `bandit14`, but the connection was still closed

`nc localhost 30000` -> connected to the service using Netcat, which accepts the raw input expected by the service

`<bandit14 password>` -> submitted the current password to the service and received the password for level 15

After logging in as `bandit14`, the goal stated that the password for level 15 could be retrieved by submitting the current password to port `30000` on the local host.

The first attempt was:

`ssh localhost -p 30000`

This failed with a connection-closed message. The initial interpretation was that the problem might still be related to the meaning of `localhost`, similar to the previous level. However, the error message `Connection closed by 127.0.0.1 port 30000` indicated that the connection was reaching the local service successfully. The problem was therefore not that `localhost` could not be reached.

A second attempt was made:

`ssh bandit14@localhost -p 30000`

This also failed. At this point, the important distinction was understood: reaching a port does not mean that the service running on that port speaks the SSH protocol.

The service on port `30000` was not an SSH server. It was designed to receive data and return a response. SSH was therefore the wrong protocol to use.

`nc`, or Netcat, was then researched as a tool for making network connections and sending raw input to services. The command was:

`nc localhost 30000`

This successfully connected to the service. The current `bandit14` password was then submitted, and the service returned the password for level 15.

What was understood:
- `localhost` refers to the local machine and resolves to the loopback address `127.0.0.1`.
- A successful connection to a port does not mean that the service on that port uses SSH.
- Different network services expect different protocols and formats of communication.
- `ssh` is specifically designed to communicate with SSH servers and cannot be used simply because a port is open.
- `nc` (Netcat) can establish TCP connections and send raw data to a listening service.
- An error such as `Connection closed by 127.0.0.1 port 30000` can indicate that the connection reached the service but the service rejected or closed the connection because the protocol or input was not what it expected.
- The hostname and port identify where a service is located, while the protocol determines how communication with that service should take place.
- Choosing the correct tool requires understanding what kind of service is running on the target port.

Vulnerability type: this level demonstrates network service enumeration and protocol-specific communication. A network port being accessible does not reveal by itself which protocol or application is operating on that port. Attempting to communicate with a service using the wrong protocol can result in the connection being rejected even when the port is reachable.

Real world version: security assessments frequently involve identifying services exposed on network ports and determining which protocols they use. An attacker or defender may encounter an open port that accepts TCP connections but does not provide the expected protocol. Understanding the distinction between a port, a service, and its communication protocol is therefore fundamental to network security.

Cloud version: cloud environments expose network services through ports controlled by security groups, firewalls, and network access controls. An exposed port may be reachable while still requiring a specific application protocol. Security testing therefore involves identifying both which ports are accessible and which services are actually listening behind them.