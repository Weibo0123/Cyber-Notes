# Process Startup & the Initial Stack

## What the Kernel Sets Up

Before your program's first instruction runs, the kernel already built an **initial stack layout** containing `argc`, `argv[]`, `envp[]`, auxiliary info (`auxv`), and the strings they point to.

```
rsp → argc
```
So at program start:
```
[rsp + 0]  = argc
[rsp + 8]  = argv[0]
[rsp + 16] = argv[1]
...
```

## Simplified Layout (high → low address)

```
Strings (program name, env strings, ...)
Auxiliary info (auxv)
envp[]  → envp[0], envp[1], ..., NULL
argv[]  → argv[0], argv[1], ..., NULL
argc    ← rsp
```

## argc / argv

- `argc` = number of command-line arguments (`./program hello world` → `argc = 3`)
- `argv` is `char *argv[]` — an **array of pointers**, not the strings themselves
- `argv[0]` = the program name/path used to invoke it
- `argv` is **NULL-terminated** — a program walks it until hitting NULL, rather than needing a stored length

## envp

Same idea as `argv`, but for environment variables:
```
NAME=VALUE
```
Also **NULL-terminated**. Comes right after `argv`'s terminating NULL:
```
argc
argv[0..N-1]
NULL
envp[0..M-1]
NULL
```
Conceptually: `envp = &argv[argc + 1]` (the `+1` accounts for `argv`'s NULL).

## Pointer vs String — the critical distinction

```
envp[0] → 0x7fffffffe100 → "FLAG=...\0"
```
`envp[0]` **is a pointer**; `*envp[0]` is the string it points to. The pointer table and the actual string data live in **separate regions** of the initial stack. Don't forget the trailing `\0` when calculating a string's total size.

## Why Stack Addresses Shift

The initial stack's exact addresses are **not fixed constants** — they depend on how much data (env vars, args) the kernel has to place.

```
Bigger environment → more stack space needed → other data shifts to lower addresses
→ rsp, argv[0], and other addresses all change
```
**Number of variables ≠ size of the environment.** `PWN=A` and `PWN=AAAAAAAAAAAAAAAA` are both "1 variable" but consume very different amounts of stack space — this can be used deliberately as **stack padding**.

## `env` and `env -i`

```bash
env                              # show current environment
env -i ./program                  # start with an EMPTY environment
env -i PWN=COLLEGE ./program        # empty environment + exactly this one var
env -i A=1 B=2 ./program              # multiple controlled vars, nothing else inherited
```
`env -i` gives you precise, reproducible control over what the program sees — useful for debugging, reproducing bugs, and exploit development.

## GDB Can Change Your Addresses!

Launching a program via `gdb ./program` → `run` may give it a **different environment** than running it directly from the shell. Different environment → different initial stack layout → different `argv[0]`, different addresses.

> **This is not GDB doing anything special to the stack.** GDB just launches the program with (possibly) a different environment, and the kernel builds the initial stack from *that* environment as usual.

If an exploit depends on exact addresses, a GDB-vs-normal-execution mismatch can make it work in GDB but fail outside it (or vice versa). Fix: use environment padding / `env -i` to **synchronize** the environments so the layouts match.

## Why It Matters Beyond GDB

The full runtime picture is bigger than just the binary:
```
Program execution =
  binary + arguments + environment + working directory
  + libraries + loader + ASLR + kernel/runtime context
```
A local dev machine's environment and a remote target's environment can differ a lot (`HOME`, `PATH`, extra service-specific vars) — this can shift addresses your exploit depends on.

## Cheat Sheet — 15 Facts to Remember

```
1. rsp initially points to argc.
2. argv/envp are arrays of POINTERS, both NULL-terminated.
3. The pointer ≠ the string it points to — strings live in a separate region.
4. C strings include a trailing \0.
5. Bigger env/args → stack shifts toward LOWER addresses.
6. Changing the environment can change rsp, argv[0], and other addresses.
7. GDB and normal execution can give the program different environments.
8. env -i clears the inherited environment for precise, reproducible control.
```
