# Text Processing Tools

These small, single-purpose tools are meant to be chained together with pipes (file 08):
```
Input → Filter/Extract → Transform → Sort/Limit → Output
```

## `grep` — Filter Lines by Pattern

```bash
grep "pattern" FILE       # print lines that match
grep -v "pattern" FILE    # print lines that DON'T match (invert)
grep -i                   # case-insensitive
grep -n                   # show line numbers
grep -r                   # recurse into directories
grep -E                   # extended regex
```
`grep` operates on whole **lines**: a line either matches or it doesn't.

## `sed` — Substitute Text Within Lines

```bash
sed "s/old/new/g"
```
```
s     = substitute
old   = pattern to find
new   = replacement
g     = global (all occurrences per line; without it, only the first match per line is replaced)
```

**`grep` vs. `sed`:** `grep`/`grep -v` *select or exclude* whole lines; `sed` *edits* text within lines without removing them.

## `tr` — Translate or Delete Characters

`tr` works on **individual characters**, not substrings:
```bash
tr O P          # every O → P
tr MA NE        # M→N, A→E  (positional mapping between the two sets)
tr -d 'abc'     # delete every a, every b, every c (NOT the substring "abc")
tr _ "\n"       # turn every _ into a newline
tr -d "\n"      # strip all newlines, joining lines together
```
Escapes like `\n` (newline) and `\\` (literal backslash) matter here — and remember shell quoting/escaping happens *before* `tr` ever sees the argument.

## `head` — Take the Beginning of a Stream

```bash
head            # first 10 lines by default
head -n 7       # first 7 lines
command | head -n 7
```

## `cut` — Extract Fields/Columns

```bash
cut -d " " -f 2      # split on space, take field 2
```
```
-d   delimiter
-f   field number (1-based — the first field is 1, not 0)
```
Example: `"hacker 78 99 67"` split on space → field 1 = `hacker`, field 2 = `78`, etc.

## `sort` — Sort Lines

```bash
sort           # lexicographic (dictionary) order by default
sort -r        # reverse
sort -n        # numeric (so 9 sorts before 10 — lexicographic order would not)
sort -u        # sort AND remove duplicate lines
sort -R        # random order
```

## Cheat Sheet

```
grep "pat" FILE          keep matching lines
grep -v "pat" FILE        keep non-matching lines
sed "s/old/new/g"          replace text within lines

tr SET1 SET2               translate characters
tr -d SET                   delete characters

head / head -n N             first line(s)
cut -d "DELIM" -f N            extract field N (1-based)
sort / -r / -n / -u / -R        sort: default / reverse / numeric / unique / random
```
