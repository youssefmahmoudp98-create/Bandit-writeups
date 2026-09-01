Goal: read the password from a file literally named -, located in the home directory.

What was done:

ls cat - -> produced no error, but no output either, and did not return control of the terminal cat ./- -> worked, password was printed

ls showed a file named -. Running cat - didn't fail outright, but it didn't do anything either — no error, no output, and the terminal didn't return to a normal prompt. Some research was needed to understand why: - is treated by cat as stdin (figured out after prior research as well), and the terminal was waiting for further input to apply that flag to, rather than treating - as a filename at all. To fix this, the filename needed to be expressed in a way that couldn't be mistaken for a flag. This led to learning that . means "in this directory" and / is the separator between directory levels, so ./- says "the file named -, in this directory" explicitly. Running cat ./- printed the password.

What was understood: - on its own can be interpreted as stdin by command-line tools rather than as a literal filename. Prefixing a filename with ./ forces it to be read as a path instead, removing that ambiguity.

Vulnerability type: special-character filename ambiguity — a case where a tool's default interpretation of an input doesn't match what was actually intended, which might lead to unexpected behaviour by engineers leading to potential security issues on the broader scale

Real world version: This level demonstrates a broader security principle: inputs can have special meanings depending on how they are interpreted by a program. In real systems, failing to distinguish user-controlled data from command or control syntax can lead to security vulnerabilities. This can appear in areas such as command injection, argument injection, path traversal, and other forms of input-handling flaws.