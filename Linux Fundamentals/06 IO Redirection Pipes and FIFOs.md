# I/O Redirection, Pipes & FIFOs

## Standard Streams and File Descriptors

Every process starts with three open **file descriptors** (small integers identifying an open I/O resource):

| FD | Name | Purpose |
|--:|---|---|
| `0` | stdin | standard input |
| `1` | stdout | standard output |
| `2` | stderr | standard error |

```
stdin (0) → [ Program ] → stdout (1)
                        → stderr (2)
```
A program doesn't need to know whether FD 0/1/2 is a terminal, a file, or a pipe — it just reads/writes to the descriptor. This uniformity is what makes redirection and pipes possible.

## Redirection Operators

```bash
command > file       # stdout → file, OVERWRITE   (same as 1> file)
command >> file      # stdout → file, APPEND      (same as 1>> file)
command 2> file      # stderr → file, overwrite
command 2>> file     # stderr → file, append
command < file       # file → stdin               (same as 0< file)
```
stdout and stderr are independent — redirecting one does not touch the other:
```bash
command > out.log 2> err.log     # two separate destinations
```

**Merging stderr into stdout** — `2>&1` redirects FD 2 to wherever FD 1 currently points:
```bash
command 2>&1 | other_command     # now BOTH streams flow into the pipe
```
`2>&1` is a file-descriptor redirection, not a pipe — order matters (`2>&1` must generally come *after* any `>` redirect that changes where stdout points, if you want stderr to follow it).

## Pipes — `|`

```bash
command1 | command2               # command1's stdout → command2's stdin
command1 | command2 | command3     # chain as many as you like
```
A pipe connects **only stdout by default**; stderr still goes to the terminal unless you also add `2>&1`.

## `tee` — Duplicate a Stream

```bash
command | tee file            # writes to file AND still passes data through to stdout
command1 | tee out.txt | command2   # save a copy mid-pipeline without breaking it
tee file1 file2                # write to multiple files (+ stdout)
tee -a file                    # append instead of overwrite
```
Unlike `command > file` (which only sends data to the file, consuming it from the pipeline), `tee` keeps the pipeline flowing while also saving a copy.

## Process Substitution — `<(...)` and `>(...)`

Makes a command's input/output available as a **file-like path**, without a named file on disk.

```bash
cat <(echo hello)          # <(...) : command's OUTPUT becomes a readable "file"
command | tee >(cmd2)       # >(...) : a "file" whose WRITES become another command's stdin
```

| Syntax | Direction |
|---|---|
| `<(command)` | command's stdout → readable file-like object |
| `>(command)` | writes to file-like object → command's stdin |

Different from a pipe: `A | B` connects stdout directly to stdin; `B <(A)` instead hands `B` a *path* it can open, read, or pass as an argument — useful when a tool wants a filename, not stdin (e.g. `diff <(cmd1) <(cmd2)`).

## FIFOs (Named Pipes)

A **FIFO** ("First In, First Out") is a named pipe — a real filesystem entry (type `p` in `ls -l`) that two independent processes can open by path to communicate:

```bash
mkfifo myfifo
# ls -l myfifo → prw-r--r--  (leading 'p' = pipe/FIFO)
```
```
Process A --write--> myfifo --read--> Process B
```

**Key property — blocking:** a writer opening a FIFO blocks until a reader also opens it (and vice versa). This gives automatic synchronization between the two sides.

| | Regular File | Unnamed pipe (`\|`) | FIFO |
|---|---|---|---|
| Purpose | persistent storage | connect two commands | named IPC channel |
| Has a filesystem path | Yes | No | Yes |
| Data persists after read | Yes | No (consumed) | No (consumed) |
| Created by | normal file creation | shell, implicitly | `mkfifo`, explicitly |

## Cheat Sheet

```
>   stdout → file, overwrite       >>  stdout → file, append
2>  stderr → file, overwrite       2>> stderr → file, append
<   file → stdin
2>&1                stderr → stdout
command1 | command2     stdout → stdin
tee file / tee -a file   duplicate stream to file(s) + stdout
<(command)                command output as a readable "file"
>(command)                writes become another command's stdin
mkfifo name                create a persistent named pipe
```
