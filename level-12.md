## Level 11 to 12

Goal: find the password stored in `data.txt`, where all lowercase and uppercase letters have been rotated by 13 positions.

What was done:

`ls`                                          -> confirmed that `data.txt` was present in the home directory

`cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'`   -> replaced each letter with the character 13 positions away and displayed the decoded password

After logging in, `ls` was used to confirm that `data.txt` was present in the home directory.

The goal stated that the contents of `data.txt` had been encoded by rotating every letter by 13 positions. The pipeline was used to pass the contents of the file into the command responsible for replacing the characters.

The `tr` command was researched and identified as a command used to translate or replace characters. The command was constructed by breaking the problem down into the character ranges that needed to be replaced.

The first set of characters was `a-zA-Z`. This represented all lowercase letters followed by all uppercase letters.

The second set needed to contain the corresponding replacement characters. For lowercase letters, `a-z` needed to be replaced by `n-z` followed by `a-m`, producing `n-za-m`. The same rotation was then applied to the uppercase letters, producing `N-ZA-M`.

The complete command was therefore constructed as `cat data.txt | tr 'a-zA-Z' 'n-za-mN-ZA-M'`. `cat data.txt` passed the contents of the file through the pipeline. `tr` then replaced each character from the first set with the corresponding character from the second set. This reversed the 13-position rotation and displayed the password.

The solution was not constructed by testing each command independently. The syntax and pipeline concepts were already understood, so the problem was instead broken down logically into the character ranges and replacements that Bash and `tr` needed, allowing the complete command to be constructed and executed directly after confirming the file with `ls`.

What was understood:
- `tr` can be used to translate or replace characters.
- Character ranges such as `a-z` and `A-Z` can be used to represent groups of characters.
- Multiple character ranges can be placed together, such as `a-zA-Z`.
- The replacement set must contain corresponding characters in the required order.
- A 13-position rotation can be represented by wrapping the end of the alphabet back to the beginning, such as `n-za-m`.
- The same transformation can be applied independently to lowercase and uppercase characters.
- `|` can be used to pass the output of `cat` into `tr`.
- A solution does not always need to be constructed through trial and error; an understood syntax can be combined with logical reasoning to construct the complete command directly.

Vulnerability type: this level demonstrates character substitution and the processing of encoded data through command-line pipelines. It also demonstrates why simple character-rotation schemes provide no meaningful confidentiality when the transformation is known or easily reversible.

Real world version: character substitution and similar encoding schemes can appear in obfuscation, legacy systems, puzzles, and poorly designed attempts to conceal sensitive information. Security analysis may require identifying the transformation being applied and reversing it to recover the original data.