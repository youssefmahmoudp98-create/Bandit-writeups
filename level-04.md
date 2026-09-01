## Level 3 to 4

Goal: read the password from a hidden file located inside the `inhere` directory.

What was done:

`ls`                    -> located `inhere` in the home directory

`cat inhere`             -> failed: `cat: inhere: Is a directory`

`file inhere`            -> confirmed `inhere` is a directory

`cd inhere`              -> entered the directory

`ls -a`                  -> showed hidden entries: `.` `..` `...Hiding-From-You`

`cat ...Hiding-From-You` -> displayed the password

After logging in, `ls` was used to inspect the home directory and locate `inhere`. The first attempt was `cat inhere`, which failed with `cat: inhere: Is a directory`. This demonstrated that `cat` is used to read files, not directories. The type of `inhere` was then confirmed with `file inhere`, which identified it as a directory.

The `cd` command was then used to enter the directory with `cd inhere`. Since the target was described as a hidden file, the appropriate way to display hidden directory entries was then determined to be `ls -a`. The output was `.`, `..`, and `...Hiding-From-You`.

The output was initially confusing because `.` and `..` have special meanings. `.` represents the current directory, while `..` represents the parent directory. `...Hiding-From-You` was the actual hidden file. Its filename begins with three dots, and the leading `.` is what causes it to be hidden from a normal `ls` listing.

The file was then read with `cat ...Hiding-From-You`, which displayed the password for the next level.

What was understood:
- A directory cannot be read with `cat` because it is a directory rather than a regular file.
- `cd` is used to change the current working directory.
- `ls -a` displays all directory entries, including hidden files. On Unix-like systems, filenames beginning with `.` are hidden from a normal `ls` listing.
- The entries `.` and `..` have special meanings: `.` refers to the current directory, `..` refers to the parent directory.
- A filename can contain multiple leading dots, so `...Hiding-From-You` is a legitimate filename consisting of three leading dots followed by `Hiding-From-You`.

Vulnerability type: this level demonstrates the importance of understanding filesystem structure and the distinction between files and directories. It also demonstrates that hidden files are not inherently protected; they are simply omitted from normal directory listings. If sensitive information is stored in a hidden file, it can still be accessed when the appropriate permissions are available.

Real world version: the same filesystem concepts appear when investigating configuration files, application directories, logs, credentials, and other files that may not appear in a standard directory listing.