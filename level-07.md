## Level 6 to 7

Goal: find the password stored somewhere on the server, owned by user `bandit7`, owned by group `bandit6`, and 33 bytes in size.

What was done:

`ls`                                      -> inspected the home directory

`ls -a`                                   -> listed all entries, including hidden files, but revealed nothing relevant

`find / -user bandit7 -group bandit6 -size 33c` -> searched the entire filesystem for a 33-byte file owned by `bandit7` and `bandit6`

`find / -user bandit7 -group bandit6 -size 33c 2>/dev/null` -> suppressed the permission-denied error messages and displayed the relevant path

`cat /var/lib/dpkg/info/bandit7.password` -> displayed the password

After logging in, `ls` was used to inspect the home directory. Nothing relevant was found, so `ls -a` was then used to include hidden entries. This also revealed nothing useful beyond the expected hidden files.

Based on the previous level, the `find` command was researched further to determine whether files could be filtered by their ownership. The `-user` and `-group` predicates were found and used to specify file ownership.

The problem stated that the password was located "somewhere in the server", so the search needed to begin from the root of the filesystem rather than from the current directory. The following command was constructed:

`find / -user bandit7 -group bandit6 -size 33c`

The `/` specified the root directory as the starting point for the search. Unlike the `.` used in previous levels, which represented the current directory, `/` caused `find` to search from the root of the filesystem and therefore traverse the directories beneath it.

The search produced a large number of permission-denied errors because the `bandit6` user did not have permission to access every directory encountered while searching the filesystem.

The errors were then redirected with:

`2>/dev/null`

The `2>` redirection targeted file descriptor 2, which represents standard error. `/dev/null` is a special device that discards anything written to it. Therefore, `2>/dev/null` caused the error messages to be discarded while allowing the normal output from `find` to remain visible.

The resulting output identified:

`/var/lib/dpkg/info/bandit7.password`

An attempt was initially made to navigate into `info` using `cd info`, but this produced `No such file or directory`. This occurred because `info` was not a directory inside the current working directory. The path displayed by `find` was an absolute path beginning from `/`, so the location needed to be referenced using that path.

A second mistake was attempting:

`cat bandit7.password`

This searched for `bandit7.password` relative to the current working directory rather than the location discovered by `find`. The path was therefore supplied explicitly:

`cat /var/lib/dpkg/info/bandit7.password`

This successfully displayed the password for the next level.

What was understood:
- `/` represents the root directory and can be used as the starting point for a filesystem-wide `find` search.
- `.` represents the current directory and limits the starting point of `find` to the current directory and its descendants.
- `-user` can be used by `find` to filter files based on their user ownership.
- `-group` can be used by `find` to filter files based on their group ownership.
- `-size 33c` restricts the search to files that are exactly 33 bytes in size.
- `2>` redirects standard error, which is file descriptor 2.
- `/dev/null` is a special device that discards data written to it.
- `2>/dev/null` therefore suppresses error messages without suppressing normal command output.
- Relative paths are interpreted from the current working directory.
- Absolute paths begin at `/` and identify a location independently of the current working directory.
- A path returned by `find` can be passed directly to another command such as `cat`.
- Unix files can be filtered according to their user and group ownership.
- A user's primary group is configured separately from the user account; in this environment, the relevant ownership relationship involved the `bandit6` group.
- Access to a file or directory is determined by Unix permissions and ownership, rather than simply by the name of the user or group.

Vulnerability type: this level demonstrates filesystem enumeration, ownership-based filtering, permission boundaries, and error handling. It also demonstrates the importance of correctly distinguishing between relative and absolute paths when processing filesystem data.

Real world version: applications, scripts, and security tools frequently search filesystems using ownership, permissions, and other attributes. Incorrect assumptions about filesystem boundaries, path resolution, or permission errors can cause searches to miss sensitive files or expose information that should not be accessible.