# Function Calls & Control Flow

## Calling Convention

Defines: how arguments are passed, where the return value goes, which registers may be clobbered vs. must be preserved.

### Argument registers (first 6 integer/pointer args)

| Arg # | Register |
|---|---|
| 1 | `rdi` |
| 2 | `rsi` |
| 3 | `rdx` |
| 4 | `rcx` |
| 5 | `r8` |
| 6 | `r9` |

(Extra arguments beyond 6 go on the stack.) **Return value** always comes back in `rax`.

### Caller-saved vs Callee-saved registers

| Caller-saved (may be freely changed by any called function) | Callee-saved (must be restored before returning) |
|---|---|
| `rax rcx rdx rsi rdi r8 r9 r10 r11` | `rbx rbp r12 r13 r14 r15` |

If the caller needs a caller-saved register's value *after* the call, it must save it itself (e.g. `push rdi ... call foo ... pop rdi`). The caller can safely assume `rbx` (etc.) is unchanged after a call, because the ABI obligates the callee to restore it.

## call / ret Mechanics

```asm
call foo    ; ≈ push return_address; jmp foo
ret         ; ≈ pop return_address; jmp return_address
```

## Register Clobbering

A register is **clobbered** when a function overwrites its previous value.

```asm
mov rax, 5
call foo        ; foo is allowed to change rax (it's caller-saved)
; rax may no longer be 5 here
```

## Function Pointers & Indirect Calls

A **function pointer** is just the address of a function.

```asm
call solve   ; DIRECT call — target known at assembly time
call rax     ; INDIRECT call — target is whatever address rax holds
```
Indirect calls are common in OOP (vtables), callbacks, dynamic libraries, and ROP chains.

---

## Comparisons & Flags

```asm
cmp a, b
```
Internally computes `a - b`, but **discards the result** — it only updates the CPU **FLAGS** register (result is not stored, operands unchanged).

The key flag: **ZF (Zero Flag)** — set to 1 if the subtraction result was zero (i.e. `a == b`).

```asm
setz dil   ; converts ZF into a 0/1 byte:  ZF=1 → dil=1,  ZF=0 → dil=0
```

## Conditional & Unconditional Jumps

```asm
jne fail   ; jump if ZF == 0 (values were NOT equal)
jmp loop   ; ALWAYS jump (unconditional)
```
`cmp` + a conditional jump normally appear together — every new `cmp` overwrites the previous FLAGS.

Typical success/fail structure:
```asm
cmp ...
jne fail
; success path
...
fail:
; failure path
```

## Memory Size Specifiers

The assembler needs to know how many bytes an instruction touches:

```asm
BYTE PTR [rax]    ; 1 byte
QWORD PTR [rsp]   ; 8 bytes
```

## Strings & Pointer Arithmetic

Strings are stored as consecutive bytes ending in a **null terminator** `\0` (not counted in the string's length).

```asm
mov rax, [rsp+16]   ; rax = address of argv[1]
BYTE PTR [rax]      ; first character
BYTE PTR [rax+1]     ; second character
BYTE PTR [rax+2]     ; third character
```

## Loops — the Three Essential Parts

Every loop needs:
1. **Current position** — usually a pointer
2. **Progress** — usually a counter
3. **Termination condition** — e.g. hitting `\0`

```asm
loop:
    ; compare
    ; conditional jump (exit if done)
    ; loop body
    jmp loop      ; backward jump
done:
```

### strlen() logic, in assembly terms

```c
int len = 0;
while (*p != '\0') { len++; p++; }
```
```
Read char → is it '\0'? → No → counter++ → pointer++ → jump back → repeat
```
This exact `cmp → conditional jump → body → jmp loop` pattern is one of the most common things you'll recognize when reverse engineering.

## Cheat Sheet

```
Args (1-6)      → rdi, rsi, rdx, rcx, r8, r9
Return value    → rax
Caller-saved    → rax rcx rdx rsi rdi r8 r9 r10 r11 (can change across calls)
Callee-saved    → rbx rbp r12-r15 (must be restored by callee)
call            → push return addr, jump
ret             → pop return addr, jump back
cmp a,b         → computes a-b, only updates FLAGS (ZF etc.), stores nothing
jne/je/jg/...   → conditional jump based on FLAGS
jmp             → unconditional jump
Loop pattern    → position + progress + termination condition
```
