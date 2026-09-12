# Filesystem, Paths & CWD

## The Filesystem Tree

Linux organizes everything under a single hierarchical tree rooted at `/`.

```
/
├── bin
├── etc
├── home
│   └── hacker
├── tmp
└── challenge
    └── run
```

**Root directory (`/`) ≠ root user.** `/` is the top of the filesystem; `root` is the superuser account. Don't confuse them.

A path is a sequence of components separated by `/`. `/challenge/run` = root → `challenge` → `run`.

## Absolute vs. Relative Paths

| Type | Starts with | Depends on CWD? |
|---|---|---|
| Absolute | `/` | No — always resolves the same |
| Relative | anything else | Yes — resolved against the Current Working Directory |

```
CWD = /tmp
a/b/file   →  /tmp/a/b/file

CWD = /
a/b/file   →  /a/b/file
```

## Current Working Directory (CWD) and `cd`

The **CWD** is the directory a shell/process is currently "in." Change it with `cd`:

```bash
cd /challenge   # CWD becomes /challenge
cd              # no argument → goes to $HOME
```

`cd` only changes the shell's own working directory — it doesn't execute a program (this is why it must be a shell builtin; see file 03).

## `.` and `..`

```
.   → current directory
..  → parent directory
```

`/challenge/./run` = `/challenge/run` (multiple `.` don't change anything).
`/tmp/a/b/..` = `/tmp/a`.

A path is resolved component by component, left to right, applying `.`/`..` as it goes.

## Home Directory and `~`

Every normal user has a home directory, e.g. `/home/hacker`. Bash gives you a shortcut:

```
~        →  /home/hacker      (tilde expansion, happens before the program runs)
$HOME    →  /home/hacker      (environment variable)
```

**`~` is not `.`.** `.` always means "wherever the CWD currently is"; `~` always means the home directory, regardless of CWD.

Only a **leading** `~` is expanded — `~/~` becomes `/home/hacker/~`, not `/home/hacker/home/hacker`.

The shell prompt often reflects the CWD, abbreviating home as `~`:
```
hacker@dojo:~$        → CWD = /home/hacker
hacker@dojo:/$         → CWD = /
hacker@dojo:~/asdf$    → CWD = /home/hacker/asdf
```

## Command Name vs. Path

These three look similar but mean different things:

| Input | Meaning |
|---|---|
| `run` | Command name → looked up via `$PATH` (see file 03) |
| `./run` | Relative path → the `run` file in the CWD |
| `/challenge/run` | Absolute path → that exact file |

Bash does **not** automatically search the CWD for bare command names — this is a deliberate security feature. That's why you always need `./` to run a program sitting in your current directory.

## Shell Expansion Happens Before the Program Runs

Bash transforms your input — expanding `~`, variables, globs, etc. — *before* the program ever sees it:

```
your input → Bash parsing/expansion → resulting arguments → program executes
```

This file focuses on path *mechanics* — how paths are written and resolved. For what actually lives at each standard location (`/etc`, `/var`, `/home`, and so on), see file 02.

## Cheat Sheet

```
/            root directory
~            home directory (tilde expansion → absolute path)
.            current directory
..           parent directory
$HOME        home directory (env var)
$PWD         current working directory (env var)

Absolute path   → starts with /, independent of CWD
Relative path   → doesn't start with /, resolved against CWD

cd DIR       change CWD
cd           go to $HOME

run              command name → $PATH lookup
./run            relative path → CWD/run
/challenge/run   absolute path
```
