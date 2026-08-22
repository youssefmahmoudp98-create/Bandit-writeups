## Level 0 to 1

Goal: get the password stored somewhere in the home directory.

What was done:

`ls`
`cat readme`

`ls` was used to see the files present, which showed a file called `readme`. `cat readme` was then used to read its contents, and the password was found there in plain text.

What was understood: `ls` and `cat` were already understood from prior documentation before this level, so no new command behavior was learned here — the level was about locating and reading a file, not about a tricky command.

Vulnerability type: sensitive information stored in a plain, readable file.

Real world version: this is what happens when a password or API key gets left sitting in a config file or accidentally committed to a public repo, the credential is right there for anyone who can read the file.

Cloud version: not yet, will fill in later.