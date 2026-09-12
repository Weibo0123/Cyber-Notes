# Bash Scripting

## Command Chaining

| Operator | Runs next command when... |
|---|---|
| `;` | always (unconditional, sequential) |
| `&&` | previous command **succeeded** (exit code `0`) |
| `\|\|` | previous command **failed** (non-zero exit code) |

```bash
command1; command2                  # both run regardless
command1 && command2 && command3     # stop at the first failure
command1 || command2                  # command2 only runs if command1 failed
```
(Exit codes are covered in depth in file 11.)

## Writing and Running a Script

```bash
#!/bin/bash
echo "Hello"
```
```bash
bash script.sh      # explicitly invoke bash — ignores the shebang, doesn't need +x
./script.sh          # direct execution — uses the shebang, REQUIRES execute permission
```
```bash
chmod +x script.sh
```
The `.sh` extension is only a convention — it's neither required to be a script nor sufficient to make one executable.

## Shebang

The first line, `#!interpreter`, tells the OS which interpreter to run the file with on direct execution (`./script.sh`).
```
#!/bin/bash
#!/bin/sh
#!/usr/bin/python3
```
Rules: it must be the **very first line**, with nothing (not even a blank line) before it.

## Script Arguments

```bash
#!/bin/bash
echo "First argument: $1"
echo "Second argument: $2"
```
```bash
bash script.sh hello world
# $1 = hello, $2 = world  ($0 is the script's own name, not counted as $1)
```

## Conditionals

```bash
if [ "$1" == "pwn" ]
then
    echo "college"
elif [ "$1" == "other" ]
then
    echo "..."
else
    echo "default"
fi
```
- `[ ... ]` (or the `test` builtin) performs the condition check — spaces around `[` and `]` are **mandatory**: `[ "$1" == "pwn" ]` is correct; `["$1" == "pwn"]` is a syntax error.
- `elif` = "else if" — has its own condition and needs `then`.
- `else` has no condition and no `then`.
- Bash checks branches top to bottom and executes only the **first** one that matches.

## Reading and Identifying Scripts

```bash
cat script.sh        # dump contents
less script.sh        # page through it
file script.sh         # identify the type by content, e.g. "Bourne-Again shell script"
                        # vs. "ELF 64-bit LSB executable" for a compiled binary
```
Because scripts are stored as plain text, you can read their logic directly — no decompiling needed.

## Cheat Sheet

```
;    &&    ||                 unconditional / on-success / on-failure chaining
bash script.sh  /  ./script.sh    run explicitly / run directly (needs +x + shebang)
chmod +x script.sh               make executable
#!/bin/bash                       shebang, must be first line

$1 $2 $3 ...                       positional arguments

if [ cond ]; then ... 
elif [ cond ]; then ...
else ...
fi

cat / less script.sh               view source
file script.sh                      identify file type
```
