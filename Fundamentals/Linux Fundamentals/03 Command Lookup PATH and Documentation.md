# Command Lookup, `$PATH` & Documentation

## How Bash Finds a Command

When you type a bare command name (no `/`), Bash searches the directories listed in the `PATH` environment variable, **left to right**, and runs the first matching executable it finds.

```bash
echo $PATH
# /usr/local/bin:/usr/bin:/bin
```

`:` separates directories. For `PATH=/usr/local/bin:/usr/bin:/bin`, typing `ls` checks, in order:
```
/usr/local/bin/ls
/usr/bin/ls
/bin/ls
```

If two directories both contain a program with the same name, the **earlier** directory in `PATH` wins.

## Inspecting and Modifying `PATH`

```bash
which cat          # shows which executable would run: /bin/cat
PATH=""            # clears PATH — bare command names stop working
PATH=/some/dir     # replaces PATH entirely
PATH=/some/dir:$PATH   # prepends a dir, keeping the rest
```

If `PATH` is empty or doesn't contain a directory, running a bare command name from it fails with `command not found` — you'd need `/bin/ls` (absolute) or `./ls` (relative) instead.

`PATH` is also used to resolve every external command *inside* a script, not just what you type interactively — so replacing `PATH` with a directory of custom scripts can silently break commands like `cat` used inside those scripts.

## Running Your Own Scripts as Commands

A script needs execute permission to run directly:
```bash
chmod +x myscript
```
If the script's directory is in `PATH`, you can then run it by name (`myscript`) instead of `./myscript` or its full path.

## Shell Builtins vs. External Commands

Not every command launches a separate program. Some are **builtins**, implemented directly inside the shell (`cd`, `read`, `help`, `export`, ...).

```
Shell
├── Builtins           (cd, read, help, export...)  — no PATH lookup, handled internally
└── External Commands  (ls, cat, grep...)            — found via PATH, run as a child process
```

**Why `cd` must be a builtin:** if `cd` ran as a separate child process, it would only change *that child's* CWD, then exit — the parent shell's CWD would be untouched. The shell has to handle it itself for it to have any effect.

```bash
type ls     # → ls is /bin/ls  (external)
type cd     # → cd is a shell builtin
help        # list builtins
help cd     # docs for a specific builtin
```

## Reading Documentation

Don't guess a command's syntax — check its docs, roughly in this order:

```
man program        → system manual (usually most complete)
program --help     → program's own built-in summary
program -h
```

**`man` page structure:**
| Section | Contents |
|---|---|
| `NAME` | Name + one-line description |
| `SYNOPSIS` | Usage syntax (`[brackets]` = optional, `...` = repeatable) |
| `DESCRIPTION` | Full explanation of behavior and options |
| `SEE ALSO` | Related man pages |

**Searching inside `man`:**
```
/keyword    search forward
?keyword    search backward
n / N       next / previous match
q           quit
```

## Command-Line Argument Anatomy

```
program [arguments]
```
Arguments aren't all the same kind:
```
find /tmp -name test.txt
       │      │     └── value for -name
       │      └── option/flag
       └── positional argument
```
- **Options/flags** — short (`-a`) or long (`--help`) forms, change behavior
- **Option arguments** — a value some options require (`-name test.txt`)
- **Positional arguments** — plain values, order matters

## Cheat Sheet

```
$PATH                list of dirs searched for bare command names, ':'-separated
which cmd             show which executable would run
type cmd               builtin, external, or alias?
chmod +x file          make a file executable

man program            full manual
program --help / -h    program's own quick help
help builtin            docs for a shell builtin

Builtin        → handled by the shell itself (cd, read, export, help)
External cmd   → separate executable, found via PATH (ls, cat, grep)
```
