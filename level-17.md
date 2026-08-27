## Level 16 to 17

Goal: retrieve the credentials for level 17 by submitting the current password to a port in the range `31000`-`32000` on the local host. First, the listening ports had to be identified, followed by determining which services supported SSL/TLS. Only one of the SSL/TLS services returned the credentials; the others simply echoed the submitted input.

What was done:

`nmap -p 31000-32000 localhost` -> scanned the specified port range, but did not provide enough service information to identify the required service

`nmap -sV 31000-32000 localhost` -> attempted service and version detection, but the target was not specified correctly

`nmap -p 31000-32000 -sV localhost` -> scanned the entire port range and performed service/version detection, revealing the relevant service on port `31790`

`openssl s_client -connect localhost:31790` -> established an SSL/TLS connection to the service and displayed the TLS handshake information

`echo "<bandit16 password>" | openssl s_client -connect localhost:31790 -quiet` -> automatically submitted the current password through the SSL/TLS connection while reducing the TLS output

`mkdir /tmp/keydir` -> created a temporary directory for storing the SSH private key

`cd /tmp/keydir` -> entered the temporary directory

`nano sshkey.private` -> created a file and stored the SSH private key inside it

`chmod 600 sshkey.private` -> restricted the permissions of the private key

`exit` -> left the `bandit16` SSH session and returned to the local machine

`scp -P 2220 bandit16@bandit.labs.overthewire.org:/tmp/keydir/sshkey.private .` -> copied the private key from the Bandit server to the current directory on the local machine

`chmod 600 sshkey.private` -> restricted the permissions of the private key on the local machine

`ssh -i sshkey.private bandit17@bandit.labs.overthewire.org -p 2220` -> connected to `bandit17` using the retrieved private SSH key

After logging in as `bandit16`, the level required the current password to be submitted to a service listening somewhere in the port range `31000` to `32000` on the local host. The level also specified that the correct service used SSL/TLS and that only one of the SSL/TLS services would provide the credentials.

The first attempt was:

`nmap -p 31000-32000 localhost`

This scanned the specified range of ports but did not provide enough information to identify which service was relevant.

The next attempt was:

`nmap -sV 31000-32000 localhost`

This was an incorrect use of the command because the port range was supplied without the `-p` option. The purpose of `-sV` was also not fully understood at first.

After researching the options, both were combined correctly:

`nmap -p 31000-32000 -sV localhost`

This scanned ports `31000` through `32000` and attempted to determine the services and versions running on the discovered ports. The output eventually identified port `31790` as the relevant service.

The scan took several minutes, which initially appeared unusual. Later research showed that Nmap timing options such as `-T4` can be used to make scans more aggressive and faster when appropriate. This was learned after the relevant port had already been identified.

The next step was to connect to the discovered service using SSL/TLS:

`openssl s_client -connect localhost:31790`

As with the previous level, `openssl s_client` produced a large amount of TLS-related information, including certificate information, session details, cipher information, and handshake output.

The password was initially entered manually into the connection. The response contained a `KEY UPDATE` message, which was initially confusing and made it unclear whether the password had been processed correctly.

The approach was then changed to use a pipeline:

`echo "<bandit16 password>" | openssl s_client -connect localhost:31790 -quiet`

`echo` supplied the current password automatically, while the pipe passed it to `openssl s_client`. The `-quiet` option reduced the amount of TLS connection output, making the service's response easier to identify.

The service responded that the password was correct and returned an SSH private key for the next level.

The next problem was determining how to save the private key. Since the key was displayed directly in the terminal, it needed to be written to a file. It was discovered that `nano` could be used to create and edit a file directly from the terminal.

The Bandit home directory was not suitable for storing the file, so a temporary directory was created:

`mkdir /tmp/keydir`

The directory was then entered:

`cd /tmp/keydir`

A new file was created with:

`nano sshkey.private`

The retrieved private key was pasted into the file and saved.

The permissions were then restricted:

`chmod 600 sshkey.private`

This was initially done out of habit from the previous level, where SSH required restrictive permissions on private keys. It was then understood that the important copy of the key for the eventual SSH connection would be the one transferred to the local machine.

The Bandit session was then exited:

`exit`

The private key was copied from the Bandit server to the local machine:

`scp -P 2220 bandit16@bandit.labs.overthewire.org:/tmp/keydir/sshkey.private .`

The current level password was supplied when `scp` requested authentication. The key was then given restrictive permissions on the local machine:

`chmod 600 sshkey.private`

Finally, the private key was used to authenticate as `bandit17`:

`ssh -i sshkey.private bandit17@bandit.labs.overthewire.org -p 2220`

This successfully established the SSH session as `bandit17`.

What was understood:
- `nmap` can be used to scan a range of network ports.
- `-p` specifies which ports Nmap should scan.
- `-sV` enables service and version detection.
- Combining Nmap options allows both port discovery and service identification in the same scan.
- Nmap scan speed can be adjusted with timing options such as `-T4`.
- An open port does not by itself identify the protocol being used by the service.
- `openssl s_client` can establish an SSL/TLS connection to a service.
- TLS connections produce substantial diagnostic output that can be separated from the application's response.
- `-quiet` can reduce the diagnostic output from `openssl s_client`.
- `echo` can be used to provide input to another command through a pipe.
- A pipeline can automate input instead of requiring it to be typed manually.
- `nano` can be used to create and edit files directly from the terminal.
- `/tmp` can be used to store temporary files when the current directory is unsuitable.
- `scp` can transfer files between systems using SSH.
- SSH private keys should have restrictive permissions such as `600`.
- A credential retrieved from one service can sometimes be used to authenticate to another service.
- When a solution becomes confusing, changing the way the input is supplied can make the service's actual response easier to understand.

Vulnerability type: this level demonstrates network service discovery, service identification, SSL/TLS communication, credential handling, and SSH private-key authentication. It combines port scanning with protocol-specific interaction and demonstrates how an exposed network service can provide sensitive authentication material.

Real world version: security assessments commonly involve scanning network ranges, identifying exposed services, determining which protocols they use, and testing how those services handle authentication. A service that unintentionally exposes credentials or private keys can provide an attacker with a path to further authenticated access.

Cloud version: the same concepts apply to cloud infrastructure where security groups, firewalls, and load balancers determine which ports are reachable. Security engineers may scan permitted infrastructure, identify exposed services, verify whether encryption is correctly configured, and investigate whether sensitive credentials or keys are exposed through network services.