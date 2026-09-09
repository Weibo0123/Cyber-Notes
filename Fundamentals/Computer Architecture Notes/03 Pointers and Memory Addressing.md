# Pointers, Memory Addressing & Partial Registers

## What Is a Pointer?

A **pointer** is a value that stores a memory **address** — not the actual data.

```
Memory
Address    Contents
133700     42
```
If `rax = 133700`, we say **`rax` is a pointer to 42**.

A pointer is not a special CPU data type — it's just a number. The CPU doesn't know whether a number is an address, an integer, or anything else. **Data is just data; meaning depends on how an instruction uses it.**

## The Golden Rule: No Brackets vs Brackets

```asm
mov rdi, rax      ; rdi = rax           (copy the value/address itself)
mov rdi, [rax]    ; rdi = memory[rax]   (dereference — read what rax points to)
```

| Instruction | Meaning |
|---|---|
| `mov rdi, rax` | Copy the value |
| `mov rdi, [rax]` | Read the value stored **at** the address in `rax` |

> **No brackets → the value/address itself. Brackets → dereference the address.**

## Reading & Writing Memory

```asm
mov rbx, [rax]     ; Read:  rbx = memory[rax]
mov [rax], rbx      ; Write: memory[rax] = rbx   (rax itself is unchanged!)
```

C equivalent:
```c
rdi = rax;      // mov rdi, rax
rdi = *rax;     // mov rdi, [rax]
rax = *(rdi+8); // mov rax, [rdi+8]
```

## Offset Addressing

```
Memory
133700  50
133701  42

mov rax, [rdi]      ; rdi=133700 → rax = 50
mov rax, [rdi+1]    ; address = rdi+1 = 133701 → rax = 42
```
Each address = 1 byte, so an offset of `N` means `N` bytes away.

## Pointer Chains (Multi-Level Indirection)

Each pair of `[ ]` removes one layer of indirection:

```asm
mov rax, [rax]   ; rax = *rax   (1st dereference)
mov rax, [rax]   ; rax = *rax   (2nd dereference)
```
```
Register → Address → Address → Data
```
Always ask: **"Am I using this value directly, or treating it as a memory address?"**

## Partial Registers

A 64-bit register can be accessed in smaller slices:

| Register | Size |
|---|---|
| `RAX` | 64 bits (8 bytes) |
| `EAX` | 32 bits (lower 4 bytes of RAX) |
| `AX`  | 16 bits (lower 2 bytes) |
| `AL`  | 8 bits  (lowest byte) |

```asm
mov rax, 0x1122334455667788   ; RAX = 1122334455667788
mov eax, 0xffffffff            ; only lower 32 bits change
                                ; RAX = 00000000ffffffff
```
**Important:** writing to a 32-bit sub-register (`eax`) automatically **zeroes the upper 32 bits** of the full 64-bit register. This does *not* happen for 16-bit (`ax`) or 8-bit (`al`) writes.

Useful for pwn — precise control over how many bytes you overwrite:
```asm
mov al, 0x41                     ; write 1 byte
mov eax, 0x41414141               ; write 4 bytes
mov rax, 0x4141414141414141        ; write 8 bytes
```

---

## RIP-Relative Addressing

`RIP` = Instruction Pointer, holds the address of the **next instruction**.

### The problem: ASLR
Modern OSes randomize where a program is loaded each run (**ASLR**). An absolute address like `mov rdi, 0x400080` may not exist after relocation → crash.

### The fix: relative addressing
Even though the whole program moves, the **distance** between code and data stays constant. So instead of "where is the data?", ask **"how far is the data from me?"**

```asm
lea rdi, [rip + 0x50]
```
If `RIP = 0x555555554010`, the CPU computes `0x555555554010 + 0x50 = 0x555555554060` — this works no matter where the program was loaded.

### LEA vs MOV — critical distinction

```asm
lea rdi, [rip + message]   ; rdi = &message        (compute address, no memory read)
mov rdi, [rip + message]   ; rdi = value at message (reads memory)
```

| | Reads memory? | Result |
|---|---|---|
| `LEA` | No | The **address** |
| `MOV [addr]` | Yes | The **value** at that address |

RIP-relative addressing is why Position-Independent Executables (PIE), shared libraries, and shellcode all work correctly regardless of load address.

## Cheat Sheet

```
Pointer         = a value that stores an address
rax             → the value/address itself
[rax]           → dereference: value stored at that address
LEA             → computes an address, never touches memory
MOV [addr]      → reads/writes memory
Writing EAX     → clears upper 32 bits of RAX
[rip + offset]  → position-independent addressing (works despite ASLR)
```
