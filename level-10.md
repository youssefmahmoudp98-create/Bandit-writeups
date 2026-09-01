## Level 9 to 10

Goal: find the password in `data.txt`, which is contained within one of the few human-readable strings preceded by several `=` characters.

What was done:

`ls` -> confirmed that `data.txt` was present in the home directory

`grep == data.txt` -> produced `grep: data.txt: binary matches`, indicating that the file was treated as binary data

`strings data.txt` -> extracted human-readable strings from the binary file

`strings data.txt | grep == data.txt` -> incorrectly supplied `data.txt` to `grep` as a file argument rather than searching the piped input

`strings data.txt | grep ==` -> filtered the extracted strings for lines containing `==` and displayed the relevant password

After logging in, `ls` was used to confirm that `data.txt` was present in the home directory.

The first attempt was `grep == data.txt`. This produced:

`grep: data.txt: binary matches`

This indicated that `data.txt` was being treated as a binary file rather than a normal text file.

The `strings` command had already been researched as a way to extract human-readable strings from binary files, so it was used next:

`strings data.txt`

The output contained many readable strings. Since the goal specified that the password was preceded by several `=` characters, `grep` was then used to filter the output.

The first attempt at combining the commands was:

`strings data.txt | grep == data.txt`

This produced the same binary-file message. The problem was that `data.txt` at the end of the command was being interpreted by `grep` as another file to search rather than as part of the input being passed through the pipe.

The command was therefore corrected to:

`strings data.txt | grep ==`

The output displayed the human-readable strings containing `==`. Among them, the string containing the password was identified.

What was understood:
- `grep` can indicate when it has detected binary data instead of treating the file as ordinary text.
- `strings` can extract human-readable strings from binary files.
- `|` passes the standard output of one command to the standard input of another.
- When input is provided through a pipe, the receiving command does not require the original filename to be specified again.
- Adding `data.txt` after `grep ==` caused `grep` to treat it as a file argument rather than searching only the piped input.
- `grep` can be used to filter the output of another command for a specific pattern.
- Previously learned commands can be combined to solve a problem when the input format changes from ordinary text to binary data.

Vulnerability type: this level demonstrates the distinction between binary and human-readable data and the importance of correctly handling command input. It also demonstrates how command pipelines can be used to transform binary data into searchable text before applying further filtering.

Real world version: binary files often contain embedded human-readable strings such as configuration values, identifiers, error messages, or other sensitive information. Security analysis tools frequently extract and search these strings when inspecting unknown binaries or potentially malicious files.
