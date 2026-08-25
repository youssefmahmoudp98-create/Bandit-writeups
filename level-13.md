## Level 12 to 13

Goal: find the password stored in `data.txt`, which is a hexdump of a repeatedly compressed file.

What was done:

`ls`                                      -> confirmed that `data.txt` was present in the home directory

`file data.txt`                           -> identified the file as ASCII text

`mkdir /tmp/newfolder`                    -> created a temporary directory to store the files produced during the extraction process

`xxd -r data.txt > /tmp/newfolder/file1`  -> reversed the hexdump and wrote the resulting binary data to `file1`

`file /tmp/newfolder/file1`               -> identified `file1` as gzip-compressed data

`gzip -d file1`                           -> attempted to decompress the gzip file but produced an unknown suffix error

`mv file1 file1.gz`                       -> renamed the file with the expected `.gz` suffix
`gzip -d file1.gz`                        -> decompressed the gzip file

`file file1`                              -> identified the resulting file as

bzip2-compressed data
`bzip2 -d file1`                          -> decompressed the bzip2 file and produced

`file1.out`
`file file1.out`                          -> identified the result as a POSIX tar archive

`mv file1.out file1.tar`                  -> renamed the archive with the `.tar` suffix

`tar -xf file1.tar`                       -> extracted the archive and revealed `data5.bin`

`file data5.bin`                          -> identified `data5.bin` as a POSIX tar archive

`mv data5.bin data5.tar`                  -> renamed the archive with the `.tar` suffix

`tar -xf data5.tar`                       -> extracted the archive and revealed the next compressed file

`file data6.bin`                          -> identified the next file as another POSIX tar archive

`mv data6.bin data6.tar`                  -> renamed the archive with the `.tar` suffix

`tar -xf data6.tar`                       -> extracted the archive and revealed `data8.bin`

`file data8.bin`                          -> identified `data8.bin` as gzip-compressed data

`mv data8.bin data8.gz`                   -> renamed the file with the expected `.gz` suffix

`gzip -d data8.gz`                        -> decompressed the gzip file and produced `data8`

`file data8`                              -> identified the final file as ASCII text

`cat data8`                               -> displayed the password

After logging in, `ls` was used to confirm that `data.txt` was present in the home directory. The next step was to inspect the file with `file data.txt`.

The output identified `data.txt` as ASCII text. This initially appeared confusing because the level specified that the file was compressed. The reason was then understood from the level description: `data.txt` was not the compressed binary file itself, but a hexadecimal dump representing the compressed data.

The first problem was therefore to reverse the hexadecimal representation and recover the actual binary file. A temporary directory was created with `mkdir /tmp/newfolder`. This provided a location to store the extracted files without modifying the original `data.txt`.

The `xxd` command was then used with the reverse option: `xxd -r data.txt > /tmp/newfolder/file1`. `xxd -r` reversed the hexadecimal dump back into its original binary representation. The `>` operator redirected the resulting output into the file `/tmp/newfolder/file1`.

An important detail was learned here: output redirection must specify the destination file, not simply the directory in which the file should be stored. The filename `file1` therefore had to be explicitly provided inside `/tmp/newfolder`.

The resulting file was inspected with `file /tmp/newfolder/file1`. The output identified it as gzip-compressed data.

The first attempt was to decompress it with `gzip -d file1`. This produced an unknown suffix error. This led to the discovery that `gzip` uses the filename suffix as part of its normal default decompression workflow and expects a `.gz` suffix.

The file was therefore renamed with `mv file1 file1.gz`, then decompressed with `gzip -d file1.gz`. The resulting file was inspected again with `file file1`. This time, the output identified the file as bzip2-compressed data.

The bzip2 file was decompressed with `bzip2 -d file1`. The resulting output was named `file1.out`. Running `file file1.out` identified it as a POSIX tar archive.

At this point, the repeated compression pattern became clear: each extraction produced another file that had to be inspected with `file` before determining the appropriate decompression or extraction command.

The tar archive was renamed with `mv file1.out file1.tar`, then extracted with `tar -xf file1.tar`. This revealed `data5.bin`. Running `file data5.bin` showed that it was another POSIX tar archive. The same process was repeated — `mv data5.bin data5.tar` then `tar -xf data5.tar` — which revealed `data6.bin`, again identified with `file` as a POSIX tar archive. It was renamed and extracted in the same way, eventually revealing `data8.bin`.

After running `file data8.bin`, the output identified it as gzip-compressed data. The file was renamed with `mv data8.bin data8.gz`, then decompressed with `gzip -d data8.gz`. The resulting `data8` file was finally inspected with `file data8`. This time, the output identified the file as ASCII text. The contents were then read with `cat data8`, which displayed the password.

What was understood:
- `file` can be repeatedly used to determine what type of data a file currently contains before deciding how to process it.
- A file being identified as ASCII text does not necessarily mean that the underlying information is plain text; in this case, the ASCII data was a hexadecimal representation of binary data.
- `xxd -r` can reverse a hexadecimal dump back into binary data.
- `>` redirects command output into a specified file.
- Output redirection requires a destination filename rather than only a directory.
- `mkdir` can be used to create a directory for temporary files.
- `gzip -d` can decompress gzip data.
- `bzip2 -d` can decompress bzip2 data.
- `tar -xf` can extract files from a tar archive.
- Filename suffixes such as `.gz`, `.bz2`, and `.tar` can affect how command-line tools identify and handle files.
- `mv` can be used to rename a file when a different filename or suffix is required.
- `file` should be used again after each extraction because the resulting file may use a completely different compression or archive format.
- A repeated process can be handled by identifying the current file type, selecting the appropriate tool, extracting it, and inspecting the result again.
- The extraction process involved several layers of different formats rather than one compression method being applied repeatedly.

Vulnerability type: this level demonstrates how file formats, compression layers, archives, and encoded representations can be chained together. It also demonstrates why identifying the actual format of a file is important before attempting to process it, particularly when file extensions cannot be trusted to accurately represent the underlying data.

Real world version: security analysts frequently encounter files that are compressed, archived, encoded, or deliberately disguised using misleading filenames or multiple layers of transformation. Correctly identifying each layer allows the analyst to safely extract and inspect the underlying data.

Cloud version: not yet explored.