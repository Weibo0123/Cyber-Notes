# Processes & Job Control

## Processes

A **process** is a running instance of a program, with its own identity and state:

| Field | Meaning |
|---|---|
| PID | Process ID — unique numeric identifier |
| PPID | Parent Process ID — who launched it |
| TTY | Controlling terminal; `?` = not attached to one |
| TIME | Total **CPU time** consumed — not real/wall-clock elapsed time |
| CMD | The command (and args) that started it |

```bash
ps -ef        # all processes, full format
ps aux        # BSD-style: all users (a), no-terminal procs too (x), user-oriented (u)
ps -efww / ps auxww    # ww = don't truncate long command lines
```
`ps aux` adds resource fields: `%CPU`, `%MEM`, `VSZ` (virtual mem size), `RSS` (resident physical mem), plus `STAT` (state, below).

## Signals

A **signal** notifies a process of an event or requests an action.

```bash
kill PID        # sends SIGTERM by default — "please terminate" (can be caught/cleaned up after)
```
```
Ctrl-C  → SIGINT   → interrupt the foreground process group (program usually exits)
Ctrl-Z  → SIGTSTP  → suspend the foreground process group (process PAUSES, doesn't exit)
```

| | Signal | Target |
|---|---|---|
| `Ctrl-C` | SIGINT | foreground process group |
| `Ctrl-Z` | SIGTSTP | foreground process group |
| `kill PID` | SIGTERM (default) | specified process |

**Stopped ≠ terminated.** A stopped process still exists and can be resumed later:
```
Running --Ctrl-Z--> Stopped --resume (bg/fg)--> Running
```

## Jobs and Job Control

A shell tracks the processes it launched as **jobs**:

| | Process | Job |
|---|---|---|
| Identified by | PID | Job ID, e.g. `[1]` |
| Scope | OS-wide | shell-local |

```
[1] 1771     →  Job 1, PID 1771  →  referred to as %1
```

**Foreground** — has terminal control, receives keyboard signals like Ctrl-C.
**Background** — keeps running while the shell stays available for other commands.

```bash
command &        # start directly in the background
fg / fg %1        # bring a job to the foreground (whether stopped or already backgrounded)
bg / bg %1         # resume a STOPPED job, keeping it in the background
```
```
command      → Running + Foreground
command &     → Running + Background
Ctrl-Z         → Stopped
Stopped --bg--> Running + Background
Background --fg--> Running + Foreground
```

## `STAT` — Process State

```bash
ps -o user,pid,stat,cmd
```
| Symbol | Meaning |
|---|---|
| `R` | Running |
| `S` | **Sleeping** (waiting on I/O/timer/event — not the same as suspended!) |
| `T` | **Stopped** (e.g. after `Ctrl-Z`) |
| `+` | belongs to the foreground process group |

`S` (sleeping) and `T` (stopped) are easy to confuse: sleeping is a normal, self-resuming wait state; stopped means execution is paused until something (`bg`/`fg`) explicitly resumes it.

## Exit Codes

Every command returns an integer exit code on completion:
```
0        → conventionally: success
non-zero → conventionally: failure (exact meaning is program-specific)
```
```bash
command
echo $?      # exit code of the MOST RECENTLY run command
```
⚠️ `$?` is overwritten by every command you run — including `echo` itself the next time — so capture it immediately if you need it later.

## Cheat Sheet

```
ps -ef / ps aux / ps -efww         list processes (various formats)
kill PID                            send SIGTERM (default)
Ctrl-C → SIGINT      Ctrl-Z → SIGTSTP

command &            start in background
fg [%N]  / bg [%N]     foreground / resume-in-background a job

STAT: R=running  S=sleeping  T=stopped  += foreground group

$?                    exit code of last command; 0 = success
```
