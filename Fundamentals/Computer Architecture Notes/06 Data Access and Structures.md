# Data Access & Reconstructing Data Structures

## Where Data Lives

| Region | Typical contents | Access pattern |
|---|---|---|
| `.data` | Initialized global/static variables | RIP-relative |
| `.rodata` | Read-only data, strings, constants | RIP-relative |
| `.bss` | Uninitialized global/static variables | RIP-relative |
| Stack | Local variables, saved registers | `rsp`/`rbp`-relative |
| Heap | Dynamically allocated (`malloc`) | Pointer-based |

**Key idea:** the compiler strips away source-level structure. A `struct Player { int health; float x; char name[32]; }` doesn't show up explicitly in the binary — you reconstruct it from offsets, access sizes, and how values are used.

## Stack Data Access

```asm
mov rax, [rsp + 0x8]    ; RSP-relative → usually positive offsets
mov rdx, [rbp - 0x20]    ; RBP-relative → often negative offsets
```
This is a common pattern, **not an absolute rule** — optimized code can use `rsp` in more complex ways or omit `rbp` entirely.

### Indirect stack access

Instead of accessing the stack directly, code may first copy the address into another register:
```asm
mov rdx, rsp
mov rax, [rdx]     ; now you must trace: "where did rdx come from?"
```
This is what makes reversing harder — pointer origins may be set several instructions (or functions) earlier.

## Global/Static Data — RIP-Relative Addressing

On x86-64, global/static data is commonly reached via `RIP + offset` rather than a hardcoded absolute address — this lets code run correctly no matter where it's loaded (works with ASLR/PIE).

```asm
mov eax, DWORD PTR [rip + 0x2f12]   ; → some global/static variable
```

### LEA vs MOV, again

```asm
mov rax, [rip + 0x1234]   ; load the DATA at that address
lea rax, [rip + 0x1234]   ; load the ADDRESS itself (no memory read)
```

## Heap Data Access

`malloc()` returns a pointer that the program must store somewhere (register, stack, global, or another heap object):

```asm
call malloc
mov [rbp - 0x8], rax     ; store the heap pointer on the stack

; later:
mov rax, [rbp - 0x8]     ; rax = the heap pointer
mov edx, [rax]           ; dereference it → read heap data
```
Because heap access always goes through a pointer somewhere, reversing heap logic requires **pointer tracking**.

## The Golden Rule (again)

```asm
mov rax, rsp    ; rax = RSP        (no brackets = value/address itself)
mov rax, [rsp]  ; rax = *(RSP)     (brackets = dereference)
```

### Two-level pointer chain

```asm
mov rax, [rsp]
mov rdx, [rax]
```
```
rsp → (pointer) → rax → (pointer) → memory → rdx
```

## Recognizing Access Patterns — a Decision Process

When you see `mov rax, [rbp - 0x18]`, don't assume it's a pointer — ask *what's stored there*: could be an int, a local var, a pointer, or a struct field.

But when you see a **chain**:
```asm
mov rax, [rbp - 0x18]
mov rdx, [rax]
```
now it's much more likely `[rbp - 0x18]` holds a **pointer**.

## Reconstructing a struct from Offsets

Given repeated accesses like:
```asm
mov eax, [rdi]
mov eax, [rdi + 4]
mov rax, [rdi + 8]
mov edx, [rdi + 0xc]
```
Build an offset table, then infer meaning from *how* each field is used:

| Offset | Size | Likely meaning |
|--:|--:|---|
| `+0x0` | 4 bytes | `int` |
| `+0x4` | 4 bytes | `int` |
| `+0x8` | 8 bytes | pointer |
| `+0xc` | 4 bytes | `int` |

Clues:
```asm
cmp eax, 100        ; field is compared to a number → probably an int
call [rdi + 8]       ; field is called as code    → probably a function pointer
```

## The Reverse-Engineering Question Chain

```
Where is the data?
  ↓
How is its address calculated?
  ↓
Is the address dereferenced?
  ↓
Where did the pointer come from?
  ↓
What is stored there, and how is it used?
```

## Cheat Sheet

```
[rsp+N] / [rbp-N]  → likely stack data
[rip+N]            → likely .data / .rodata / .bss (global/static)
LEA [addr]         → RAX = the address
MOV [addr]         → RAX = the value at that address
mov rax, rdx       → copy (RAX = RDX)
mov rax, [rdx]     → dereference (RAX = *RDX)
Struct fields      → reconstructed from offsets + access size + usage
```
