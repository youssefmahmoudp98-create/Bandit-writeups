## Level 17 to 18

Goal: find the password for level 18, which is the only line that has been changed between `passwords.old` and `passwords.new`.

What was done:

`ls` -> confirmed that `passwords.old` and `passwords.new` were present in the home directory

`diff passwords.old passwords.new` -> compared the two files and displayed the line that had changed

The output immediately revealed the password for the next level.

After logging in, `ls` was used to inspect the home directory. The files `passwords.old` and `passwords.new` were present.

The level stated that the password for the next level was the only line that had been changed between the two files.

The `diff` command had already been researched and understood from the recommended commands in previous Bandit levels, so no new command concepts were required.

The two files were compared directly with:

`diff passwords.old passwords.new`

`diff` compared the contents of the two files and displayed the difference between them. Since the level specified that only one line had been changed, the output directly revealed the line containing the password for the next level.

What was understood:
- `diff` can be used to compare the contents of two files and identify differences.
- Previously researched commands can become immediately useful when a later level is built around the same concept.
- Not every level requires a new command or technique; sometimes the challenge is recognizing which previously learned tool directly matches the problem.
- Understanding what a command does is more useful than simply remembering its syntax.

Vulnerability type: this level demonstrates file comparison and the importance of identifying unintended changes between files. In security contexts, unexpected differences between configuration files, scripts, credentials, or other sensitive files can reveal unauthorized modifications or information disclosure.

Real world version: file comparison is commonly used when reviewing configuration changes, investigating suspicious modifications, comparing versions of scripts, and detecting unauthorized changes to important files.

Cloud version: the same concept applies to cloud configuration and infrastructure files. Comparing known-good configurations against current versions can help identify unauthorized or unexpected changes to cloud resources, deployment files, policies, and other security-sensitive configurations.