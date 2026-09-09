# Regular Expressions

## The `re` Module

**Regular expressions (regex)** are patterns for searching, validating, cleaning, and extracting text.
```python
import re

if re.search(r".+@.+", email):     # returns a match object, or None
    print("Valid")
```

## Common Regex Symbols

| Pattern | Meaning |
|---|---|
| `.` | Any character except newline |
| `*` | 0 or more repetitions |
| `+` | 1 or more repetitions |
| `?` | 0 or 1 repetition |
| `{m}` / `{m,n}` | Exactly `m` / between `m` and `n` repetitions |
| `^` / `$` | Start / end of string |
| `[]` / `[^]` | Character set / negated character set |
| `\|` | Either A or B (alternation) |
| `(...)` / `(?:...)` | Capturing group / non-capturing group |

Example: `^.+@.+\.edu$` — start, ≥1 chars, literal `@`, ≥1 chars, literal `.edu`, end.

### Character Classes (Shorthand Sets)
```
\d digit    \D not-digit    \s whitespace    \S non-whitespace    \w word char    \W non-word char
```
`\w` is a shorter way of writing `[a-zA-Z0-9_]`.

### Escaping and Raw Strings
`.` normally means "any character" — to match a **literal** period, escape it: `\.`
```python
re.search(r"\.edu", email)     # matches ".edu", not "?edu"
```
Because regex uses backslashes heavily, Python **raw strings** (`r"..."`) prevent Python itself from interpreting `\` as an escape sequence before the regex engine even sees it.

### Groups, Optional Patterns, Alternation
```regex
(\w+\.)?                       optional group — the ? makes the WHOLE group optional
^\w+@(\w+\.)?\w+\.edu$            matches both malan@harvard.edu and malan@cs50.harvard.edu
(com|edu|gov|net|org)              alternation — matches any one of these
```

## Flags

```python
re.search(pattern, string, re.IGNORECASE)     # case-insensitive
re.MULTILINE
re.DOTALL
```
Related functions: `re.match()`, `re.fullmatch()`.

## Cleaning Up User Input

```python
name = input("What's your name? ").strip()

if matches := re.search(r"^(.+), *(.+)$", name):     # walrus: assign AND test in one line
    name = matches.group(2) + " " + matches.group(1)   # "Malan, David" → "David Malan"
```
- `group(1)`/`group(2)` — the captured text before/after the comma.
- `matches.groups()` — retrieve all captured groups at once: `last, first = matches.groups()`.
- The walrus operator `:=` assigns a value *while* evaluating it as a condition — avoids a separate assignment line before the `if`.

## Extracting Data

**`re.sub()`** — replace matches:
```python
username = re.sub(r"https://twitter.com/", "", url)
```
**`removeprefix()`** — simpler alternative for a fixed, known prefix (only strips it if it's actually at the start, unlike a blind `.replace()`):
```python
username = url.removeprefix("https://twitter.com/")
```

**Extracting a username from a URL:**
```python
if matches := re.search(
    r"^https?://(?:www\.)?twitter\.com/([a-z0-9_]+)",
    url,
    re.IGNORECASE
):
    print(matches.group(1))
```
- `https?` — matches `http` or `https`
- `(?:www\.)?` — optional `www.`, **not** captured (non-capturing group)
- `([a-z0-9_]+)` — the actual capture: the username

## Cheat Sheet

```
re.search(pattern, string, flags)     find a match anywhere; returns match object or None
re.match() / re.fullmatch()             match at start / match the whole string
re.sub(pattern, repl, string)              replace matches
re.IGNORECASE                                 case-insensitive flag

. * + ? {m} {m,n} ^ $ [] [^] | () (?:)      core symbols
\d \D \s \S \w \W                              shorthand character classes
r"..."                                            raw string — needed for regex backslashes

group(n) / groups()      retrieve captured text
:=                          walrus — assign + test in one expression
```
