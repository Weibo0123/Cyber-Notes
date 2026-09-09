# Terminal Multiplexing — `screen` & `tmux`

## The Idea

A **terminal multiplexer** lets you run multiple terminal sessions/windows inside one terminal, and — critically — keeps them running even after you disconnect.

```
Terminal
  └── screen / tmux
        └── Session
             ├── Window 0 → shell/process
             ├── Window 1 → shell/process
             └── Window 2 → shell/process
```
- **Session** — the persistent overall environment
- **Window** — one terminal inside a session (think browser tabs)
- **Detach ≠ Exit** — detaching leaves the session running in the background; exiting the shell inside it ends the session

## `screen`

```bash
screen                # start a new session
```
```
Ctrl-A d      detach (session keeps running)
```
```bash
screen -ls             # list sessions → e.g. "23851.goodwork" = PID.name
screen -r               # reattach (only one session running)
screen -r goodwork       # reattach to a specific session, by name or PID
```
Windows:
```
Ctrl-A c      create a window
Ctrl-A n      next window
Ctrl-A p      previous window
Ctrl-A 0–9    jump to window 0–9
Ctrl-A "      window picker menu
```

## `tmux`

Same concepts, different prefix key: **`Ctrl-B`** instead of `Ctrl-A`.

```bash
tmux                  # start a new session
```
```
Ctrl-B d      detach
```
```bash
tmux ls                       # list sessions
tmux attach  /  tmux a          # reattach
```
Windows:
```
Ctrl-B c      create window
Ctrl-B n      next window
Ctrl-B p      previous window
Ctrl-B 0–9    jump to window 0–9
Ctrl-B w       window picker
```
tmux also shows a status bar at the bottom, e.g. `[0] 0:bash* 1:bash` — the `*` marks the currently active window.

## `screen` vs `tmux`

| | `screen` | `tmux` |
|---|---|---|
| Start | `screen` | `tmux` |
| Prefix | `Ctrl-A` | `Ctrl-B` |
| Detach | `Ctrl-A d` | `Ctrl-B d` |
| List sessions | `screen -ls` | `tmux ls` |
| Reattach | `screen -r` | `tmux attach` / `tmux a` |
| New/next/prev window | `Ctrl-A c/n/p` | `Ctrl-B c/n/p` |
| Window picker | `Ctrl-A "` | `Ctrl-B w` |

## Cheat Sheet

```
screen                              tmux
├── Start:  screen                  ├── Start:  tmux
├── Prefix: Ctrl-A                  ├── Prefix: Ctrl-B
├── Detach: Ctrl-A d                ├── Detach: Ctrl-B d
├── List:   screen -ls              ├── List:   tmux ls
├── Attach: screen -r [name]        ├── Attach: tmux attach / tmux a
├── New win: Ctrl-A c               ├── New win: Ctrl-B c
├── Next/Prev: Ctrl-A n / p         ├── Next/Prev: Ctrl-B n / p
└── Picker: Ctrl-A "                └── Picker: Ctrl-B w
```
**Core idea:** Session → Window → Process, and it all survives detaching. Remember `screen = Ctrl-A`, `tmux = Ctrl-B`.
