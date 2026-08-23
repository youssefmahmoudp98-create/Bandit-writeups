## Level 2 to 3

Goal: read the password from a file named `--spaces in this filename--`, located in the home directory.

What was done:

`ls`                                       -> confirmed the exact filename
`cat --spaces in this filename--`          -> failed: unrecognized option
`cat -- --spaces in this filename--`       -> failed: still split on spaces
`cat "-- --spaces in this filename--"`     -> failed: `--` no longer acted as a terminator
`cat "./--spaces in this filename--"`      -> worked
`cat -- "--spaces in this filename--"`     -> also worked

`ls` was used first to confirm the exact filename. The first attempt, running `cat` with the filename typed directly, failed because the filename starts with `--`, which the shell treats as the start of an option flag rather than as part of a filename — `cat` reported an unrecognized option.

The second attempt added a standalone `--` before the filename, since `--` normally tells a command "stop treating anything after this as an option." This stopped the leading `--` in the filename from being read as a flag, but the filename still wasn't quoted, so the shell split it into several separate arguments wherever there was a space — the fix for one problem didn't touch the other.

The third attempt tried wrapping the entire thing, including the leading `--`, inside one pair of quotes: `cat "-- --spaces in this filename--"`. This also failed, and understanding why mattered: `--` only works as an option-terminator when the shell passes it to `cat` as its own separate argument. Wrapped inside the same quotes as the filename, the whole string becomes a single argument, so `cat` never sees a standalone `--` at all — it just sees one argument whose text happens to start with those characters, with no special meaning attached. The leading `--` had already lost its ability to function as a terminator the moment it was merged into the same quoted block as the rest of the name, rather than being its own token.

The working fix needed the two problems solved separately, not together in one string: `cat "./--spaces in this filename--"` used `./` to force the whole thing to be read as a path rather than an option, while `cat -- "--spaces in this filename--"` used a standalone, unquoted `--` as its own argument to terminate option parsing, followed by the quoted filename as a second, separate argument that preserved the spaces. Both worked.

What was understood: quoting prevents word-splitting on spaces, but it does not on its own stop leading dashes from being read as an option — that requires either `./` or a standalone `--` passed as its own argument, not folded into the same quotes as the filename. `--` only behaves as a flag-terminator when the shell sees it as a distinct argument by itself; once it's part of a larger quoted string, it's just literal text with no special behavior, which is exactly why the third attempt failed even though it looked like it should work.

Vulnerability type: special-character filename ambiguity — the same underlying category as level 1 to 2, compounded here by two independent forms of ambiguity (option-flag interpretation and word-splitting) that had to be resolved separately, and by a subtler trap where a fix that looks correct (wrapping everything in one set of quotes) actually removes the very mechanism it was meant to rely on.

Real world version: this extends the level 1 to 2 lesson — a program's default parsing of input not matching what was intended — by showing that a plausible-looking fix can still be wrong if it misunderstands *how* the protective mechanism actually works, not just whether it's present. In real systems, this maps to input sanitization that looks correct on the surface but fails because the escaping or boundary logic isn't applied where the parser actually needs it.

Cloud version: not yet, will fill in later.