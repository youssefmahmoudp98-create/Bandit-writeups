## Level 13 to 14

Goal: find the password for level 14, stored in `/etc/bandit_pass/bandit14`, which can only be read by the `bandit14` user. Instead of being given the password directly, the level provides a private SSH key.

What was done:

`ls` -> inspected the home directory and found `HINT` and `sshkey.private`

`cat HINT` -> displayed the instructions explaining that the password was stored in `/etc/bandit_pass/bandit14` and that an SSH key was provided instead of the password

`cat sshkey.private` -> displayed the contents of the private SSH key

`ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220` -> attempted to connect directly to `bandit14` using the private key, but the connection was blocked because SSH connections from the Bandit server itself are restricted

`exit` -> left the `bandit13` SSH session and returned to the local machine

`scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .` -> copied the private SSH key from the Bandit server to the current directory on the local machine

`chmod 600 sshkey.private` -> restricted the private key so that only the owner could read and write it

`ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220` -> connected to `bandit14` using the private SSH key

`cat /etc/bandit_pass/bandit14` -> displayed the password for the next level

After logging in as `bandit13`, `ls` was used to inspect the home directory. Two files were present: `HINT` and `sshkey.private`.

The `HINT` file was read with `cat HINT`. It explained that the password for the next level was stored in `/etc/bandit_pass/bandit14`, that the file could only be read by `bandit14`, and that an SSH private key was provided instead of the password. It also indicated that connections to localhost were blocked and recommended paying attention to error messages.

The private key was then inspected with `cat sshkey.private`. Its contents appeared as a large block of text with standard `BEGIN` and `END` markers surrounding encoded data. This was initially unfamiliar, so the concept of an SSH private key was researched.

It was understood that an SSH private key is a credential used for authentication. Unlike a password, which is generally entered as text, the private key is stored in a structured file containing key material. The format uses standard `BEGIN` and `END` markers surrounding encoded data.

The reason for the localhost restriction was also researched. Allowing players to create unrestricted SSH connections from the Bandit server could allow them to chain sessions and consume additional server resources. Restricting these connections helps prevent unnecessary resource consumption such as CPU, memory, and network usage.

An SSH connection was then attempted directly from the Bandit server:

`ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220`

This repeatedly failed. It was initially suspected that the server might simply be blocking the literal hostname `localhost`. Further testing showed that the restriction was based on the source of the connection rather than the specific name used to refer to the server. In other words, changing the hostname did not bypass the restriction because the connection was still originating from the Bandit server.

At this point, the mental model was reset by exiting the SSH session:

`exit`

The solution was to copy the private key from the Bandit server to the local machine and then establish the SSH connection from there. `scp` was researched and used for this purpose.

The command used was:

`scp -P 2220 bandit13@bandit.labs.overthewire.org:sshkey.private .`

`scp` uses SSH to securely copy files between systems. The `-P 2220` option specified the SSH port, while `bandit13@bandit.labs.overthewire.org:sshkey.private` specified the remote user, server, and file to copy. The final `.` represented the current directory on the local machine, which was the destination for the copied file.

The private key was then used to connect to `bandit14`:

`ssh -i sshkey.private bandit14@bandit.labs.overthewire.org -p 2220`

The `-i` option specified `sshkey.private` as the identity file to use for authentication. An SSH key fingerprint confirmation appeared during the connection process, which was accepted.

Before the key could be used successfully, its permissions had to be corrected. SSH rejected the key when it was considered too accessible. This led to the understanding that private keys must be protected from other users because anyone able to read the private key could potentially use it as an authentication credential.

The permissions were therefore changed with:

`chmod 600 sshkey.private`

This restricted the file so that the owner could read and write it, while other users had no permissions on the file.

After the permissions were corrected, the SSH connection succeeded. Once logged in as `bandit14`, the password could finally be read:

`cat /etc/bandit_pass/bandit14`

This displayed the password for the next level.

What was understood:
- An SSH private key is a credential that can be used for authentication instead of a password.
- Private SSH keys use a standard file format with `BEGIN` and `END` markers surrounding encoded key data.
- `ssh -i` specifies an identity file to use for authentication.
- SSH private keys must have appropriately restrictive permissions to prevent other users from reading them.
- `chmod 600` gives the owner read and write permissions while removing permissions for group and other users.
- `scp` can securely copy files between systems using SSH.
- `scp -P` specifies the SSH port used for the connection.
- `.` represents the current directory and can be used as the destination for `scp`.
- SSH connections can be restricted based on where the connection originates, rather than simply blocking a particular hostname or string.
- Error messages can reveal important information about why a connection or command failed.
- Sometimes solving a problem requires changing the environment in which a command is executed rather than changing the command itself.
- SSH authentication can replace password-based authentication when the appropriate private key is available.

Vulnerability type: this level demonstrates authentication through SSH private keys, file permission security, and the importance of protecting private credentials. It also demonstrates how restricting the source of network connections can prevent users from chaining authenticated sessions through a server.

Real world version: private SSH keys are commonly used to authenticate administrators, developers, CI/CD systems, and cloud infrastructure. If private keys are exposed or stored with excessive permissions, an attacker who obtains them may be able to authenticate as the associated user. Restricting SSH access by source and enforcing appropriate key permissions are therefore important security controls.

Cloud version: SSH keys are commonly used to authenticate to cloud virtual machines and other infrastructure. The same concepts apply to cloud servers where private keys must be protected, permissions must be restricted, and network controls can limit which sources are allowed to establish SSH connections.