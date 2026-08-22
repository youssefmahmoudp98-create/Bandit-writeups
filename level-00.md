## Level 0

Goal: log into the Bandit server for the first time using the given credentials.

What was done:

`ssh bandit0@bandit.labs.overthewire.org -p 2220`

This follows the format `ssh user@host -p port`. `bandit0` is the username, `bandit.labs.overthewire.org` is the server's address, and `-p 2220` tells SSH which port to connect on.

What was understood: a port is not a location, it is which specific program on the machine the connection is meant for, the IP address gets a message to the right machine, and the port gets it to the right program running on that machine. SSH assumes port 22 by default, so any server running SSH on a different port (Bandit uses 2220) requires the port to be specified explicitly with `-p`, otherwise the connection attempt goes to the wrong door and fails.

Vulnerability type: none, this level is just the login step, no vulnerability involved.

Real world version: not applicable yet, this level was setup only.

Cloud version: not yet, will fill in later.
