## Level 7 to 8

Goal: find the password for the next level in `data.txt`, next to the word `millionth`.

What was done:

`ls`                                      -> confirmed that `data.txt` was present in the home directory

`grep "millionth" data.txt`               -> searched `data.txt` for the line containing `millionth` and displayed the password

After logging in, `ls` was used to confirm that `data.txt` was present in the home directory.

The goal specified that the password was located next to the word `millionth`. Since `grep` had already been understood as a tool for searching text and displaying the matching line, it was used directly:

`grep "millionth" data.txt`

The command searched `data.txt` for the string `millionth` and displayed the entire line containing the match. The password was located on that same line, so no further investigation was required.

What was understood:
- `grep` can be used to search the contents of a file for a specified string.
- By default, `grep` displays the entire line containing the matching text.
- Previously learned commands can be applied directly when the requirements of a new level match an already-understood concept.
- Not every level requires a new command or technique; recognizing when an existing mental model is sufficient is also part of efficient problem solving.

Vulnerability type: this level demonstrates basic text searching and the importance of efficiently locating specific information within potentially large files.

Real world version: text-searching tools such as `grep` are commonly used when inspecting logs, configuration files, source code, and other large text-based datasets. Searching for a known identifier or pattern can quickly isolate relevant information without manually inspecting the entire file.
