# Linux x86-64 Syscalls in Practice

## The Universal Convention

```
rax  = syscall number
rdi  = 1st argument
rsi  = 2nd argument
rdx  = 3rd argument
r10  = 4th argument
r8   = 5th argument
r9   = 6th argument
syscall   ; triggers the call
```
The return value comes back in `rax`. **Almost every Linux syscall follows this pattern.**

## Immediate Value vs Memory Address (recap)

```asm
mov rdi, 133700      ; rdi = 133700 (just a number — no memory access)
mov rdi, [133700]     ; rdi = memory[133700] (dereference — read what's stored there)
```

---

## exit — syscall 60

```asm
mov rdi, 42     ; exit status
mov rax, 60     ; syscall number for exit
syscall
```
Equivalent to `exit(42);`. Takes one argument (`rdi`).

---

## read — syscall 0

```c
read(fd, buffer, count);
```
```asm
mov rax, 0       ; syscall: read
mov rdi, 0       ; fd = 0 (stdin)
mov rsi, rsp      ; buffer = stack address
mov rdx, 128       ; max bytes to read
syscall
```

### The stack as a buffer
`rsp` points to readable+writable memory, so it doubles as a convenient temporary buffer:
```asm
mov rsi, rsp   ; "the buffer is the stack"
```

### Return value = actual bytes read (not the requested count!)

```
Requested: 128 bytes    Actual input: "Hello" (5 bytes)
→ after syscall, rax = 5
```
`read` does **not** always fill the requested amount — always check `rax` for the real count.

### Save rax before it gets overwritten

```asm
; after read: rax = bytes read (e.g. 57)
mov rdx, rax     ; save it! (rdx = 57)
mov rax, 1        ; now safe to reuse rax for the next syscall (write)
```

---

## write — syscall 1

```c
write(fd, buffer, count);
```
```asm
mov rax, 1        ; syscall: write
mov rdi, 1         ; fd = 1 (stdout)
mov rsi, buffer     ; address of the data
mov rdx, count       ; number of bytes to write
syscall
```

### File descriptors

| fd | Name | Purpose |
|--:|---|---|
| 0 | stdin | Standard input |
| 1 | stdout | Standard output |
| 2 | stderr | Standard error |
| 3+ | — | Files opened via `open` |

### write does NOT know string length

Unlike `printf`, `write` never scans for `\0` — it **only trusts `rdx`**.
```
Memory: "HelloWorld"
rdx=5  → outputs "Hello"
rdx=10 → outputs "HelloWorld"
```

---

## open — syscall 2

```c
int fd = open("/flag", 0);
```
```asm
mov rax, 2                 ; syscall: open
mov rdi, filename_address    ; address of the filename string
mov rsi, 0                    ; flags
syscall
```
`rax` after the call = the new **file descriptor** (or `-1` on failure — check filename address, that the string exists, and that it ends in `\0`).

### Writing a filename manually into memory

```asm
mov BYTE PTR [rsp], '/'
mov BYTE PTR [rsp+1], 'f'
mov BYTE PTR [rsp+2], 'l'
mov BYTE PTR [rsp+3], 'a'
mov BYTE PTR [rsp+4], 'g'
mov BYTE PTR [rsp+5], 0      ; null terminator — required!
mov rdi, rsp                  ; tell open: "the filename starts here"
```
`BYTE PTR` matters because the assembler otherwise doesn't know if you mean to write 1, 4, or 8 bytes.

### Getting a filename from argv instead

```asm
mov rdi, [rsp+16]   ; argv[1] → pointer to the filename string
mov rax, 2
mov rsi, 0
syscall
```

### Debugging open()

```bash
strace ./program
# openat("/flag", O_RDONLY) = 3     → success (fd 3)
# openat(...) = -1                   → check filename/args
```
```gdb
x/s $rsp     # verify the string actually written on the stack is correct
```

---

## Full open → read → write → exit Example

```asm
.intel_syntax noprefix

; write filename "/flag\0" onto the stack
mov BYTE PTR [rsp], '/'
mov BYTE PTR [rsp+1], 'f'
mov BYTE PTR [rsp+2], 'l'
mov BYTE PTR [rsp+3], 'a'
mov BYTE PTR [rsp+4], 'g'
mov BYTE PTR [rsp+5], 0

; open("/flag", 0)
mov rax, 2
mov rdi, rsp
mov rsi, 0
syscall

; read(fd, buffer, 100)
mov rdi, rax     ; fd returned by open
mov rax, 0
mov rsi, rsp
mov rdx, 100
syscall

; write(1, buffer, bytes_read)
mov rdx, rax      ; bytes_read from read's return value
mov rax, 1
mov rdi, 1
syscall

; exit(42)
mov rax, 60
mov rdi, 42
syscall
```

## Cheat Sheet

```
Syscall numbers:  read=0   write=1   open=2   exit=60
Convention:       rax=number, rdi/rsi/rdx/r10/r8/r9 = args 1-6, rax=return value
read()  → rax = actual bytes read (may be less than requested)
write() → trusts rdx exactly; does NOT look for \0
open()  → filename needs a trailing \0; rax = fd (or -1 on failure)
BYTE PTR [addr], val   → write exactly one byte
```
