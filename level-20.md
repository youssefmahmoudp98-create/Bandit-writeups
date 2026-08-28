## Level 19 to 20

Goal: use the setuid binary in the home directory to gain access to the password for level 20, stored in `/etc/bandit_pass/bandit20`.

What was done:

`ls -la` -> listed all files with their permissions, ownership, sizes, and other details

`./bandit20-do` -> executed the setuid binary and displayed instructions for how to use it

`./bandit20-do cat /etc/bandit_pass/bandit20` -> executed `cat` through the setuid binary with the privileges of `bandit20`, displaying the password

After logging in as `bandit19`, `ls -la` was used to inspect the home directory in detail.

Among the output, the following entry stood out:

`-rwsr-x--- 1 bandit20 bandit19 14880 bandit20-do`

The output was initially unfamiliar, so the format of `ls -la` and the meaning of the permission string were researched.

The first character, `-`, indicated that the entry was a regular file.

The next three characters, `rws`, represented the owner's permissions. The owner had read and write permissions, while the `s` indicated a special setuid permission.

The following three characters, `r-x`, represented the group's permissions.

The final three characters, `---`, represented the permissions for everyone else.

The `1` represented the number of hard links to the file. `bandit20` was the owner, `bandit19` was the group, `14880` was the file size, and `bandit20-do` was the filename.

The `s` in the owner's execute position was the important part. It indicated that the setuid bit was enabled. When an executable with setuid permission is executed, it runs with the effective user ID of the file's owner rather than simply the privileges of the user who launched it.

This meant that although the current user was `bandit19`, executing `bandit20-do` caused the binary to operate with the privileges of `bandit20`.

The distinction between normal execute permission and setuid execution was then understood. The `x` permission allows a user to execute a program, but the program normally operates with that user's privileges. The setuid `s` changes the effective user identity under which the executable runs.

The file was then executed from the current directory:

`./bandit20-do`

The `./` specified that `bandit20-do` should be executed from the current directory. Running it without an argument displayed instructions explaining how the binary could be used.

Following those instructions, the password file was read through the setuid binary:

`./bandit20-do cat /etc/bandit_pass/bandit20`

The `cat` command was executed through `bandit20-do`, causing it to run with the effective privileges of `bandit20`. This allowed the password file, which was otherwise inaccessible to `bandit19`, to be read successfully.

What was understood:
- `ls -la` displays detailed information including permissions, ownership, file size, and hidden files.
- The first character of a permission string indicates the file type.
- `r`, `w`, and `x` represent read, write, and execute permissions.
- The permission groups correspond to the owner, group, and others.
- The `s` in the owner's execute position indicates the setuid permission.
- A setuid executable runs with the effective user ID of its owner.
- Normal execute permission allows a program to run with the executing user's privileges, while setuid can cause it to run with the owner's privileges.
- `./` specifies a file located in the current directory.
- A binary is an executable program rather than simply a file with execute permissions.
- The owner and group of a file are separate from the permissions assigned to them.
- A setuid binary can perform privileged operations on behalf of a less-privileged user.

Vulnerability type: this level demonstrates a setuid privilege escalation. A setuid executable that performs privileged operations based on user-controlled arguments can potentially allow a lower-privileged user to execute commands or access files with the privileges of the binary's owner.

Real world version: setuid binaries are a legitimate Unix/Linux mechanism for allowing specific programs to perform privileged operations. However, insecure setuid programs can become privilege-escalation vulnerabilities if they execute arbitrary commands, trust user-controlled input, or fail to restrict what operations the invoking user can perform.

Cloud version: the same privilege-boundary concepts apply to cloud Linux instances and containers. A vulnerable privileged executable can allow a compromised low-privileged process or account to escalate its permissions and access credentials, configuration files, or other resources that should be restricted.