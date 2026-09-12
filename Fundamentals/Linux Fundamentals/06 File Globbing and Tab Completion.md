# File Globbing & Tab Completion

## Shell Globbing

**Globbing** is filename/path pattern-matching performed by the shell — patterns are expanded **before** the command runs (same "shell expansion" principle as `~` in file 01).

| Pattern | Meaning |
|---|---|
| `*` | any number of characters (including zero) |
| `?` | exactly one character |
| `[abc]` | one character from the set `a`, `b`, `c` |
| `[!abc]` / `[^abc]` | one character *not* in the set |

## `*` — Any Number of Characters

```bash
file_*
```
matches `file_a`, `file_b`, `file_hello`, `file_123` — one file, many files, or (by default) zero files, in which case Bash leaves the pattern unchanged (`echo nope_*` → literally prints `nope_*` if nothing matches).

Restrictions: `*` does **not** match `/`, and does not match a leading `.` in a filename by default (so `*` alone skips dotfiles).

## `?` — Exactly One Character

```bash
file_?     # matches file_a, file_b — not file_cc
file_??    # matches file_cc (two characters)
```

## `[]` — Character Sets

```bash
file_[ab]      # matches file_a, file_b — not file_c
file_[pwn]     # matches file_p, file_w, file_n
```
Negate with `!` or `^` as the **first** character inside the brackets:
```bash
[!abc]   # anything except a, b, c
[^abc]   # same meaning
```
(`!` only has this negating meaning when it's the very first character in `[]`.)

## Path Globbing

Globs apply to full paths too, matched component by component (since `*` can't cross `/`):
```bash
/home/hacker/file_[ab]     # → /home/hacker/file_a, /home/hacker/file_b
/ho*/*ck*                  # can match /home/hacker
```

## Tab Completion

Pressing `Tab` auto-completes commands, filenames, and paths based on what's already typed:
```bash
cat /challenge/pwn<TAB>    # → cat /challenge/pwn.college
```
With multiple matches (e.g. `flag`, `flamingo`, `flowers` all starting with `f`), Bash completes to the **longest common prefix** (`fl`), then on a second `Tab` typically lists all the options (exact behavior can vary by shell config).

## Globbing vs. Tab Completion

Both help you refer to files without typing everything, but they work differently:
- **Globbing** — a *pattern* that can match multiple existing files at once, resolved when the command runs.
- **Tab completion** — an *interactive* shortcut that helps you type one specific existing path faster.

## Cheat Sheet

```
*         any number of characters (not / , not leading .)
?         exactly one character
[abc]     one char from the set
[!abc]    one char NOT in the set  (also [^abc])
Tab       auto-complete; multiple matches → longest common prefix, then list
```
