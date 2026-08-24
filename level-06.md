Level 5 to 6

Goal: find the password stored in a human-readable file that is 1033 bytes in size and is not executable.

What was done:

"ls -a"                         -> inspected the home directory and located "inhere"
"cd inhere"                     -> entered the directory
"ls"                            -> listed "maybehere00" through "maybehere20"
"file -l maybehere*"            -> produced a large amount of output because "-l" has a command-specific meaning and is not a universal "long format" option
"file maybehere*"              -> identified the "maybehere" entries as directories
"find . -type f -exec file {} \; | grep -i "ascii"" -> searched for regular files and filtered their "file" output for ASCII text
"find . -type f -size 1033c"   -> located the file with a size of exactly 1033 bytes
"cat ./<filename>"              -> displayed the password
"find . -type f -size 1033c ! -executable -exec file {} \; | grep -i "ascii"" -> combined the known conditions into a single search

After logging in, "ls -a" was used to inspect the home directory and locate "inhere". The "cd" command was then used to enter the directory with "cd inhere".

The contents were listed with "ls", which displayed "maybehere00" through "maybehere20".

The first attempt was "file -l maybehere*". The "-l" option was initially assumed to have a similar purpose to the "-l" option used with "ls", but this produced a large amount of output containing many file signatures. This demonstrated that command-line options are interpreted by the individual programs receiving them and do not have universal meanings across commands.

The next attempt was "file maybehere*". The "*" wildcard was used to target all entries matching the "maybehere" pattern. The output showed that the entries were directories, meaning the files being searched for were located inside them.

After reaching this point, "find" was researched as a way to search through the directory structure. The following command was then used: "find . -type f -exec file {} \; | grep -i "ascii"".

The "find" command was used to search for entries. The "." represented the current directory, which was "inhere".

"-type f" restricted the results to regular files.

"-exec file {} \;" caused the "file" command to be executed for each file found by "find". "{}" represented the current file being processed. The "\;" marked the end of the "-exec" expression. The semicolon was escaped so that Bash did not interpret it as shell syntax and instead passed it to "find" as part of the expression.

The output from "file" was then passed through the pipe to "grep -i "ascii"". "grep" filtered the output for occurrences of "ascii", while "-i" made the search case-insensitive. This allowed the human-readable file to be identified from the output produced by "file".

The size requirement was then applied with "find . -type f -size 1033c". The previous "find" concepts remained the same, while "-size 1033c" restricted the search to files with a size of exactly 1033 bytes. The "c" specified that the size was measured in bytes.

The resulting file was then read with "cat ./<filename>", which displayed the password.

Out of curiosity, all of the conditions were then combined into a single command:

"find . -type f -size 1033c ! -executable -exec file {} \; | grep -i "ascii""

The only new concept in this command was "!". In this context, "!" negated the following "find" test. Therefore, "! -executable" meant that the file must not be executable.

What was understood:

- Command-line options are interpreted by the individual programs receiving them and do not have universal meanings.
- "find" can be used to search through a directory structure using multiple conditions.
- "." represents the current directory when used as the starting point for "find".
- "-type f" restricts "find" to regular files.
- "-exec" allows a command to be executed for each result produced by "find".
- "{}" represents the current file being passed to the command executed by "find".
- "\;" marks the end of the "-exec" expression, with the semicolon escaped so Bash does not interpret it.
- "file" can be used to determine the type and characteristics of a file.
- "grep -i" can filter command output for a specific string without considering letter case.
- "-size 1033c" specifies a file size of exactly 1033 bytes.
- "!" can negate a "find" condition, allowing "! -executable" to specify files that are not executable.
- "*" can act as a wildcard in shell filename expansion, matching a sequence of characters.
- Multiple conditions can be combined into a single "find" command instead of checking each requirement separately.

Vulnerability type: this level demonstrates how command-line tools interpret options, filenames, wildcards, and other special syntax. Incorrect assumptions about how a command interprets its arguments can lead to unexpected behavior, particularly when filenames or other inputs are controlled externally.

Real world version: the same concepts appear in scripts and applications that process filenames or other command-line arguments. Incorrect handling of special characters, wildcards, options, or user-controlled input can cause programs to interpret data as control information or operate on unintended files.

Cloud version: not yet explored.