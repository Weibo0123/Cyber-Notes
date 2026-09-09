# Linux Signals

## What Is a Signal?

An **asynchronous** notification sent to a process by the kernel or another process.

```
System call:  Process → Kernel   (process requests something)
Signal:       Kernel/Process → Process   (something notifies the process)
```
A signal can tell a process to terminate, stop, continue, or run a handler.

## Signal Numbers to Memorize

| Signal | # | Common cause |
|---|--:|---|
| `SIGINT` | 2 | `Ctrl+C` |
| `SIGKILL` | 9 | Immediate, forced termination |
| `SIGSEGV` | 11 | Invalid memory access (segfault) |
| `SIGALRM` | 14 | Timer set by `alarm()` |
| `SIGTERM` | 15 | Request termination (can be caught) |
| `SIGCONT` | 18 | Continue a stopped process |
| `SIGSTOP` | 19 | Force-stop a process |
| `SIGTSTP` | 20 | `Ctrl+Z` |
| `SIGWINCH` | 28 | Terminal window resized |

```
Ctrl+C       → SIGINT
Ctrl+Z       → SIGTSTP
kill -9 PID  → SIGKILL
Segfault     → SIGSEGV
alarm()      → SIGALRM
```

## Handlers vs Default Action

A process can install a **handler**:
```c
void handler(int sig) { printf("Received %d\n", sig); }
signal(SIGINT, handler);
```
```
Signal arrives → has a handler? → Yes: run handler | No: default action (ignore/terminate/stop/continue)
```

## The Two Uncatchable Signals

```
SIGKILL = 9
SIGSTOP = 19
```
These **cannot** be caught, blocked, or ignored — they always take effect immediately. Contrast:

| SIGTSTP (20) | SIGSTOP (19) |
|---|---|
| `Ctrl+Z`, **catchable** | `kill -19 PID`, **uncatchable** |

`SIGCONT` (18) resumes a stopped process: `kill -19 PID` (stop) → `kill -18 PID` (resume). Shell `bg`/`fg` use this mechanism too.

## SIGSEGV & Pwn

```c
int *p = NULL;
*p = 123;   // invalid memory access → SIGSEGV → default action: terminate
```
> **A "segmentation fault" IS the process receiving `SIGSEGV`.** When your exploit crashes a program, don't just think "it crashed" — think "the process received SIGSEGV," then go inspect registers/stack in GDB.

## SIGALRM & Timeouts

```c
alarm(3);                     // kernel starts a 3-second timer
signal(SIGALRM, handler);      // optional: catch it instead of dying
```
Without a handler, `SIGALRM`'s default action terminates the process — commonly used to implement timeouts.

## Sending Signals

```bash
kill -SIGNAL PID       # e.g. kill -9 PID, kill -19 PID
kill -SIGKILL PID       # can also use names
kill -l                 # list all signals
pgrep program_name       # find a PID
```

## Process Termination Paths

```
Process ends via:
  1. exit()                                  → terminate
  2. signal whose default action terminates   → terminate (e.g. SIGSEGV, SIGKILL)
```
After termination, a process can remain a **zombie** until the parent calls `wait()`/`waitpid()`.

## Cheat Sheet

```
SIGINT=2  SIGKILL=9  SIGSEGV=11  SIGALRM=14  SIGTERM=15  SIGCONT=18  SIGSTOP=19  SIGTSTP=20
Uncatchable: SIGKILL, SIGSTOP
Segfault  = SIGSEGV received
strace ./program   → shows signals alongside syscalls
```
