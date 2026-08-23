## Level 4 to 5

Goal: read the password from the only human-readable file located inside the `inhere` directory.

What was done:

`ls`                    -> located `inhere` in the home directory

`cd inhere`             -> entered the directory

`ls -a`                 -> listed all entries, including `.` `..` and `-file00` through `-file09`

`cat -- "-file01"`      -> read one file successfully, but did not contain the password

`file ./-file*`         -> identified the types of all files matching the pattern

`cat -- "-file07"`      -> displayed the password

After logging in, `ls` was used to inspect the home directory and locate `inhere`. The `cd` command was then used to enter the directory with `cd inhere`.

Since the directory contents needed to be inspected, `ls -a` was used. The output contained `.` and `..`, along with the files `-file00` through `-file09`. The position of `.` and `..` in the output was different from the previous level, but their meanings remained the same: `.` represents the current directory and `..` represents the parent directory.

The files all began with `-`, so the knowledge from the previous level was applied. A standalone `--` was used to prevent the filenames from being interpreted as options: `cat -- "-file01"`. This allowed the file to be read, but manually checking every file would have been inefficient.

The `file` command was then researched and used to determine the type of each file. Since all filenames followed the same `-file` pattern, the wildcard `*` was tested to determine whether the pattern could be used to target all of them at once: `file ./-file*`. The shell expanded `*` to match the filenames beginning with `-file`, allowing `file` to report the type of each file. Among the results, `-file07` was identified as the ASCII text file, making it the relevant file to inspect.

The file was then read with `cat -- "-file07"`, which displayed the password for the next level.

What was understood:
- `file` can be used to determine the type of a file.
- `*` can act as a wildcard in shell filename expansion, matching any sequence of characters.
- A previously learned concept can be tested in a new context rather than treating each command as isolated syntax.
- The `./` prefix can be used to explicitly treat filenames beginning with `-` as paths.
- `--` can be passed as a separate argument to indicate the end of options, allowing filenames beginning with `-` to be handled safely.
- `.` and `..` retain their special meanings regardless of their position in a directory listing.

Vulnerability type: this level demonstrates several command-line parsing concepts that can become relevant to security when filenames or other inputs are handled incorrectly. It combines option interpretation with filename pattern matching, demonstrating why the exact way input is parsed by the shell and the receiving program matters.

Real world version: the same concepts appear in scripts and applications that process user-controlled filenames or other command-line arguments. Incorrect handling of leading dashes, wildcards, or other special input can cause a program to operate on unintended files or interpret data as control information.

Cloud version: not yet explored.