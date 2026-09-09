# The Stack (x86-64)

## What Is the Stack?

The stack is **not special hardware** — it's just an ordinary region of the process's virtual memory. It only becomes "the stack" because the register `rsp` (**Stack Pointer**) is used, by convention, to track its current top.

```
rsp = 0x8000   → the current top of the stack is at address 0x8000
```

### The one rule you must never forget

```asm
mov rax, rsp     ; rax = the ADDRESS stored in rsp
mov rax, [rsp]   ; rax = the VALUE stored at that address
```
```
rsp   → an address
[rsp] → the value at that address
```

## The Stack Grows Downward

The stack grows toward **lower** memory addresses.

```
Higher addresses
0x1060
0x1058
0x1050   ← old rsp
0x1048   ← new rsp (after a push)
Lower addresses
```

```
push → rsp decreases (rsp -= 8)
pop  → rsp increases (rsp += 8)
```

## push / pop

```asm
push rax
```
is conceptually:
```asm
sub rsp, 8
mov [rsp], rax
```

```asm
pop rbx
```
is conceptually:
```asm
mov rbx, [rsp]
add rsp, 8
```

Stack follows **LIFO** (Last In, First Out):
```asm
mov rax, 10 \n push rax   ; stack: 10
mov rax, 20 \n push rax   ; stack: 20 (top), 10
pop rbx                    ; rbx = 20
pop rcx                    ; rcx = 10
```

**Important:** `push` saves the current *value* in a register, not the register itself. The CPU doesn't remember that a popped value "came from rax."

## Stack Addressing & Offsets

```asm
[rsp]        ; memory at rsp
[rsp + N]    ; a HIGHER address (toward caller / older data)
[rsp - N]    ; a LOWER address
```
Most stack values are 8 bytes, so entries are usually 8 bytes apart.

## Stack Frame at Program Startup

Before a program's own function frames exist, the OS places launch info on the stack:

```
rsp → argc          [rsp]
      argv[0]        [rsp+8]
      argv[1]        [rsp+16]
      argv[2]        [rsp+24]
```
The stack stores **pointers** to the argument strings, not the strings themselves.

## call and ret

```asm
call foo
```
internally does:
```
push return_address
jmp foo
```

```asm
ret
```
internally does:
```
pop rip     ; (mov rip, [rsp]; add rsp, 8)
```
> **If `rsp` is wrong when `ret` executes, the CPU will jump to whatever garbage value happens to be there.**

## Stack Frames

A **stack frame** = the portion of stack memory a function uses while running. It can contain: local variables, temporary data, buffers, saved registers, saved `rbp`, the return address.

**There is no single fixed layout.** It depends on compiler, optimization level, ABI, and whether `rbp` is used as a frame pointer. Don't memorize `[rsp+0x08] = always X` — determine the real layout from the actual program.

## Creating & Releasing Stack Space

```asm
sub rsp, 256   ; allocate 256 bytes of stack space
...
add rsp, 256   ; release it
```

### Critical distinctions

| Operation | Does NOT mean |
|---|---|
| `sub rsp, N` (allocate) | ≠ initialize memory to zero |
| `add rsp, N` (deallocate) | ≠ erase the memory |
| Function returns | ≠ its stack bytes disappear |

Old data left behind is called **stale stack data**. A later function can reuse the same memory and may read leftover secrets from a previous function — this is a real source of information disclosure bugs.

## No CPU-Enforced Frame Boundaries

This is one of the most important ideas for binary exploitation:

> **There is no CPU concept of "stack frame ownership."** If a virtual address is accessible, any code can read or write it — regardless of which function is "supposed to" own that memory.

So caller data (usually reachable via `[rsp + offset]`, higher addresses) and old callee data (often via `[rsp - offset]`, lower/stale addresses) can both potentially be read. **This is not a universal rule — always verify from the actual layout.**

## The Stack as an Array

A function can deliberately use its frame as a buffer:

```asm
sub rsp, 256
mov byte ptr [rsp + rcx], 1   ; ≈ table[rcx] = 1  (base + index addressing)
```
A 256-byte table is a common size because one byte can represent exactly 0–255 (256 values) — useful for byte-indexed lookup tables.

## Cheat Sheet

```
Stack           = ordinary process virtual memory, tracked via rsp
Direction       = grows toward LOWER addresses
push            → rsp -= 8            pop → rsp += 8
rsp             → address              [rsp] → value
[rsp + N]       → higher address       [rsp - N] → lower address
call            → pushes return addr, jumps to function
ret             → pops [rsp] into rip
sub rsp, N      → allocate (≠ zero it)
add rsp, N      → deallocate (≠ erase it)
Stack frame     = logical region used by a function; layout NOT fixed
Cross-frame     = no CPU-enforced boundary; any accessible address can be read
Stack as array  = sub rsp, N + [rsp + index] = table[index]
```
