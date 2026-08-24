## Level 8 to 9

Goal: find the password in "data.txt", where the password occurs only once.

What was done:

"ls"                         -> confirmed that "data.txt" was present in the home directory

"sort data.txt"              -> sorted the contents so identical lines were placed next to each other

"uniq -u"                    -> filtered the sorted output to display only lines that were not duplicated

"sort data.txt \| uniq -u"   -> combined both commands and displayed the password

After logging in, "ls" was used to confirm that "data.txt" was present in the home directory.

The level indicated that commands such as "sort" and "uniq" might be useful. Both commands had already been researched and understood from previous learning, so no new command concepts were required.

"sort data.txt" was first used to arrange the lines in the file. This placed identical lines next to each other, which was important because "uniq" compares adjacent lines rather than searching the entire file for duplicates.

"uniq -u" was then used to display only lines that occurred uniquely in the sorted output.

The two commands were combined using a pipe:

"sort data.txt | uniq -u"

The output immediately displayed the password.

What was understood:

- "sort" arranges lines of text into sorted order.
- "uniq" operates on adjacent duplicate lines.
- "uniq -u" displays only lines that are not repeated.
- Sorting the input first allows duplicate lines that were originally separated to become adjacent.
- "|" passes the standard output of one command directly into the standard input of another.
- Previously learned commands can be combined to solve a problem without introducing a new technique.

Vulnerability type: this level demonstrates efficient processing of structured text through command composition. It also demonstrates how individual command-line utilities can be combined to transform and filter data.

Real world version: the same pattern is useful when processing logs, datasets, command output, and other large collections of text. Sorting data before filtering or deduplicating it can make it possible to identify unique or anomalous entries efficiently.

Cloud version: not yet explored.