# Shell Variables & Expansion

## Assignment and Expansion

```bash
VAR=1337          # NO spaces around =
echo $VAR         # → 1337
```
`VAR` refers to the variable itself; `$VAR` triggers **expansion** to its value. Variable names are **case-sensitive** (`PWN`, `pwn`, `Pwn` are three different variables).

`VAR = 1337` (with spaces) is **wrong** — Bash parses that as running a command called `VAR` with arguments `=` and `1337`.

`echo $PWD` shows the current working directory (same value as `pwd`, exposed as a variable).

## Quoting

Spaces normally separate words. To store a value containing spaces, quote it:
```bash
VAR="1337 SAUCE"
echo "$VAR"        # → 1337 SAUCE
```
Without quotes, `VAR=1337 SAUCE` does not assign the full string to `VAR` as intended.

## Local vs. Environment Variables

A plain assignment (`VAR=1337`) is **local** to the current shell — a child process (e.g. running `sh`) does **not** inherit it by default.

```
Parent Shell
     └── Child Shell/Process    ← ordinary local vars NOT passed down
```

`export` promotes a variable to an **environment variable**, which *is* inherited by children:
```bash
VAR=1337
export VAR
# or in one line:
export VAR=1337
```

| | Current shell | Child process |
|---|---|---|
| Local variable | Yes | No |
| Exported (environment) variable | Yes | Yes |

## Reading Input — `read`

```bash
read VAR                    # blocks, waits for a line of input, stores it in VAR
read -p "Enter name: " NAME # show a prompt first
```
`read`'s input source is **stdin**, so it composes naturally with input redirection:
```bash
read VAR < some_file        # reads the file's first line into VAR, no extra process needed
```

## Command Substitution

Captures a command's **stdout** as a value:
```bash
OUTPUT=$(echo Hello)        # OUTPUT = "Hello"
$(command1 $(command2))     # nesting is fine
VAR=`command`                # older backtick syntax — same idea, harder to nest/read
```
Prefer `$(...)` over backticks in modern scripts.

A common (slightly wasteful) pattern:
```bash
VAR=$(cat some_file)        # works, but spawns a whole extra `cat` process
read VAR < some_file        # equivalent for a single line, no extra process
```

## Cheat Sheet

```
VAR=value              assign (no spaces around =)
echo $VAR               expand / print value
VAR="a b c"              quote to preserve spaces
$PWD                     current directory (env var)

export VAR               make VAR inherited by child processes
export VAR=value         same, in one line

read VAR                 read a line from stdin into VAR
read -p "prompt: " VAR    prompt first
read VAR < file           read from a file via redirection

VAR=$(command)            capture command's stdout
VAR=`command`             older equivalent syntax
```
