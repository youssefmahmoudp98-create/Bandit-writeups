## Level 2 to 3

Goal: read the password from a file named `--spaces in this filename--`, located in the home directory.

What was done:

`ls`                                       -> confirmed the exact filename

`cat --spaces in this filename--`          -> failed: unrecognized option

`cat -- --spaces in this filename--`       -> failed: still split on spaces

`cat "-- --spaces in this filename--"`     -> failed: `--` no longer acted as a terminator

`cat "./--spaces in this filename--"`      -> worked

`cat -- "--spaces in this filename--"`     -> also worked

`ls` was used first to confirm the exact filename. The first attempt, running `cat` with the filename typed directly, failed — but not because the shell itself treated `--spaces` as an option. The shell's role here is to split the unquoted text on spaces and pass the resulting pieces to `cat` as separate arguments: `--spaces`, `in`, `this`, `filename--`. It was `cat` itself, once it received `--spaces` as one of its arguments, that interpreted it as an unrecognized option flag. The shell parses and hands off arguments; the program decides what those arguments mean.

The second attempt added a standalone `--` before the filename, since `--` is a convention many command-line programs (including `cat`) recognize as meaning "everything after this is a literal argument, not an option." This stopped `cat` from reading the leading `--` in the filename as a flag, but the filename still wasn't quoted, so the shell still split it into several separate arguments on the spaces — the fix for one problem didn't touch the other.

The third attempt tried wrapping the entire thing, including the leading `--`, inside one pair of quotes: `cat "-- --spaces in this filename--"`. This also failed, and understanding why mattered: the shell passes `--` to `cat` as its own distinct argument, and it's `cat` that recognizes that specific argument as the end-of-options marker. Wrapped inside the same quotes as the filename, the whole string becomes a single argument instead, so `cat` never receives a standalone `--` argument at all — it just receives one argument whose text happens to start with those characters, with no special meaning attached, since the end-of-options convention only applies to `--` as its own separate argument.

The working fix needed the two problems solved separately, not together in one string: `cat "./--spaces in this filename--"` used `./` to force the whole thing to be read as a path rather than as something starting with an option-like character, while `cat -- "--spaces in this filename--"` passed a standalone `--` as its own argument, followed by the quoted filename as a second, separate argument that preserved the spaces. Both worked.

What was understood: the shell's job (splitting on spaces, handling quotes) and a program's job (deciding what its received arguments mean, including recognizing `--` as an end-of-options marker) are two separate layers. Quoting controls what the shell hands off as arguments; it does not control how the receiving program interprets the text within a given argument. `--` only functions as an end-of-options marker when the shell passes it to `cat` as its own distinct argument — once it's folded into the same quoted argument as the filename, it's just literal text, which is exactly why the third attempt failed even though it looked like it should work.

Vulnerability type: special-character filename ambiguity — the same underlying category as level 1 to 2, compounded here by two independent forms of ambiguity (option interpretation and word-splitting) that had to be resolved separately, and by a subtler trap where a fix that looks correct (wrapping everything in one set of quotes) actually removes the very mechanism it was meant to rely on.

Real world version: this extends the level 1 to 2 lesson — a program's interpretation of input not matching what was intended — by showing that a plausible-looking fix can still be wrong if it misunderstands *how* a protective mechanism actually works, not just whether it's present. In real systems, this maps to input sanitization that looks correct on the surface but fails because the escaping or boundary logic isn't applied at the layer the parser actually reads it at.