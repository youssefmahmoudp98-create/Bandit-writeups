## Level 10 to 11

Goal: find the password stored in "data.txt", which is encoded in base64.

What was done:

"ls"                         -> confirmed that "data.txt" was present in the home directory

"cat data.txt | base64 -d"  -> passed the contents of "data.txt" to "base64" and decoded the base64-encoded text

After logging in, "ls" was used to confirm that "data.txt" was present in the home directory.

The goal stated that the password was stored in the file using base64 encoding. The "base64" command was researched to determine how to decode the encoded text. The "-d" option was found to decode base64 input back into its original text representation.

The command was then constructed using a pipeline:

"cat data.txt | base64 -d"

"cat data.txt" read the contents of the file and passed them through the pipe as standard output. The "base64 -d" command then received that input and decoded it. The decoded output contained the password.

What was understood:

- The "base64" command can be used to encode and decode base64 data.
- The "-d" option tells "base64" to decode the input.
- "|" passes the output of one command to the input of another.
- "cat data.txt" can be used to read a file and pass its contents through a pipeline.
- No new major concept was required for this level; the main addition was understanding the "-d" option of "base64".

Vulnerability type: this level demonstrates how encoded data can be transformed back into its original representation using standard command-line utilities. Base64 is an encoding mechanism rather than encryption, meaning encoded data can be decoded when the encoding method is known.

Real world version: base64 is frequently used to represent data in applications, APIs, configuration files, authentication mechanisms, and other systems. Treating base64-encoded data as if it were encrypted can lead to sensitive information being exposed when the encoded value is decoded.

Cloud version: not yet explored.