## Level 20 to 21

Goal: use the setuid binary `suconnect` to retrieve the password for level 21. The binary connects to the local host on a specified port, reads a line of text from the connection, compares it to the password for `bandit20`, and, if the password is correct, sends back the password for `bandit21`.

What was done:

`ls -la` -> inspected the home directory and identified the `suconnect` binary

`nc -l 4444` -> started Netcat and created a listener on port `4444`

`Ctrl + Z` -> suspended the Netcat process so the shell could be used again

`bg` -> resumed the suspended Netcat process in the background

`jobs` -> confirmed that the Netcat listener was running as a background job

`./suconnect 4444` -> started the setuid binary and instructed it to connect to port `4444`

`Ctrl + Z` -> suspended `suconnect` so the shell could be used to manage the Netcat listener

`fg %1` -> brought the Netcat listener back to the foreground

`<bandit20 password>` -> supplied the current password through the Netcat connection

`Ctrl + Z` -> suspended the Netcat process after sending the password

`fg %2` -> brought the suspended `suconnect` process back to the foreground so it could continue processing the submitted password

`Ctrl + Z` -> suspended `suconnect` after it verified the password and sent the response

`fg %1` -> brought the Netcat listener back to the foreground, where the password for level 21 was displayed

After logging in, `ls -la` was used to inspect the home directory and identify the available files and their permissions. The `suconnect` binary stood out as the relevant executable.

The level explained that `suconnect` connects to the local host on a port supplied as an argument, reads a line of text from that connection, compares it against the password for `bandit20`, and sends back the password for `bandit21` if the comparison succeeds.

This meant that a listener had to be created first. The listener would provide the connection for `suconnect` and allow the current password to be supplied to the binary.

Netcat was used to create the listener:

`nc -l 4444`

The `-l` option instructed Netcat to listen for an incoming connection on port `4444`.

Since the shell was occupied by the running Netcat process, `Ctrl + Z` was used to suspend it. The `bg` command was then used to resume the suspended process in the background.

`jobs` was used to verify that the listener was still running as a background job.

With the listener running, the `suconnect` binary was started:

`./suconnect 4444`

The binary attempted to connect to the listener on port `4444`.

At this point, job control became the main challenge. Both the listener and `suconnect` needed to remain active, while the shell could only have one foreground process at a time.

`Ctrl + Z` was therefore used to suspend `suconnect`, after which:

`fg %1`

was used to bring the Netcat listener back into the foreground.

The `bandit20` password was then entered into the Netcat connection. This sent the password through the connection to `suconnect`.

The Netcat process was then suspended again with `Ctrl + Z`, allowing the second process to be resumed:

`fg %2`

This brought `suconnect` back into the foreground. It continued processing the password that had been supplied through the connection, verified that it was correct, and indicated that it would send the password for the next level.

The binary then needed to remain running while the response was retrieved through Netcat. The `suconnect` process was suspended with `Ctrl + Z`, and the Netcat listener was brought back to the foreground with:

`fg %1`

The Netcat connection then displayed the password for level 21.

The main difficulty in this level was therefore not the binary itself, but coordinating two processes that needed to communicate while using Bash job control. The process had to be suspended and resumed repeatedly so that each side of the connection could be given the required input or allowed to continue executing.

What was understood:
- `suconnect` is a setuid binary designed to communicate with another process over a local TCP connection.
- A listening service can be created with `nc -l <port>`.
- A program can connect to a specific port by supplying that port as an argument.
- Netcat can be used to create simple TCP listeners and connections.
- `Ctrl + Z` suspends the currently running foreground process.
- `bg` resumes a suspended process in the background.
- `fg` brings a background or suspended job back to the foreground.
- `jobs` displays the jobs currently managed by the shell.
- Job numbers can be supplied to `fg`, such as `fg %1` and `fg %2`.
- A foreground process occupies the shell until it exits or is suspended.
- Background processes can continue running while the shell is available for other commands.
- Two processes can communicate through a network connection even though they are separate processes.
- The direction of data flow matters: Netcat supplied the password to `suconnect`, while `suconnect` returned the next password through the same connection.
- The level required coordinating process execution rather than simply executing one command after another.
- A setuid binary can perform an operation using the privileges associated with its owner, which is why its behavior must be considered from a privilege-boundary perspective.

Vulnerability type: this level demonstrates privilege boundaries involving setuid binaries and inter-process network communication. A privileged binary that accepts externally supplied input and performs sensitive operations based on that input can become dangerous if the input is not properly validated.

Real world version: privileged Unix/Linux programs sometimes communicate with other local processes through sockets, pipes, or other IPC mechanisms. If a privileged program trusts data received through such a channel without properly validating it, a lower-privileged process may be able to influence privileged operations.

Cloud version: the same principle applies to cloud-hosted Linux systems and containers where privileged processes communicate with other processes through local sockets or network interfaces. An insecure privileged service can become an escalation path from a lower-privileged process to sensitive credentials, files, or administrative functionality.