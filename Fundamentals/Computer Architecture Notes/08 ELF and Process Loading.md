# ELF Format & Process Loading

## What Is a Process?

A **program** is a file on disk. A **process** is a running instance of it — with a PID, memory space, file descriptors, parent/child relationships, etc.

## fork() vs execve() — different things!

| `fork()` | `execve()` |
|---|---|
| Creates a **new** child process | **Replaces** the program running inside the current process |

```
bash → fork() → child process → execve("./cat") → cat
```

## How Does Linux Decide What to Execute?

Linux does **not** rely on file extensions. It examines the file's **contents** (magic bytes):

- **Shebang `#!`** — e.g. `#!/bin/bash` tells the kernel to hand the file to an interpreter. Shebangs can even chain recursively (script2 → script1 → interpreter).
- **binfmt_misc** — a Linux mechanism that maps other magic-byte signatures to interpreters.
- **ELF magic number**: `7f 45 4c 46` (`\x7fELF`) → recognized as an ELF binary.

## ELF Memory Sections

| Section | Contents |
|---|---|
| `.text` | Machine instructions / executable code (R+X, not writable) |
| `.data` | Initialized global/static variables |
| `.rodata` | Read-only data — string literals, constants |
| `.bss` | Uninitialized global/static variables (zeroed at startup; **doesn't take up space in the file itself** — the loader maps and zeroes it) |

```c
int x = 10;      // .data
static int y;    // .bss (uninitialized)
"Hello world"    // .rodata
```

## Static vs Dynamic Linking

**Static** — all needed library code is baked into the executable. Bigger file, fewer runtime dependencies.

**Dynamic** — the executable depends on shared libraries (`.so` files) loaded at runtime. A dynamic ELF specifies an **interpreter** (the dynamic linker), typically:
```
/lib64/ld-linux-x86-64.so.2
```
Check it with:
```bash
readelf -l ./program   # look for "Requesting program interpreter"
```

### What the dynamic linker (`ld-linux`) does

```
find libraries → load them → resolve symbols → apply relocations → initialize → main()
```

## Library Search & Interposition

| Variable | Purpose |
|---|---|
| `LD_LIBRARY_PATH` | Adds extra directories to search for shared libraries |
| `LD_PRELOAD` | Loads a specified `.so` **before** normal resolution — lets you replace/intercept functions like `read()`, `malloc()`, `printf()` |

## Relocation

When a library's actual load address differs from what was assumed at build time, address-dependent data must be **adjusted** — this process is called relocation.

## Virtual Memory

Each process has its own virtual address space (translated by the CPU/MMU via page tables to physical RAM). Simplified layout, high → low address:

```
Kernel space
Stack (grows down)
Heap  (grows up)
Shared libraries
Program / ELF (.text, .rodata, .data, .bss)
```

Two processes can both use address `0x400000` without conflict — they map to different physical memory. This underlies **process isolation**.

Inspect it with:
```bash
cat /proc/self/maps
```
or in GDB:
```gdb
info proc mappings
```

## PIE vs ASLR — not the same thing

| PIE | ASLR |
|---|---|
| Makes an executable *capable* of being loaded at different addresses | The mechanism that actually *randomizes* load addresses (stack, heap, libraries, PIE base) |

They work together: PIE is what makes the executable *relocatable*; ASLR is what actually *scrambles* the addresses each run.

## Initialization Before main()

Programs can run **constructor** code before `main()`:
```c
__attribute__((constructor))
void init() { ... }
```
```
load / initialize → constructors → main()
```
Combined with `LD_PRELOAD`, a preloaded library's constructor can run *before the target program's own `main()`* — a key technique in dynamic analysis and CTFs.

## Full Loading Flow

```
./program → shell fork() → child → execve()
  → kernel checks file: #! / binfmt_misc / ELF
  → ELF: static or dynamic?
       dynamic → ld-linux → find/load libs → relocation
  → virtual memory set up → initialization → constructors → main()
```

## Cheat Sheet

```
fork()        → new process
execve()      → replace program image
#!            → script interpreter
Magic number  → identifies file format (ELF = 7f 45 4c 46)
.text/.data/.rodata/.bss  → code / init'd data / read-only data / uninit'd data
ld-linux      → dynamic linker/loader
LD_PRELOAD    → preload & intercept a shared library
PIE ≠ ASLR    → PIE = relocatable;  ASLR = randomizes addresses
readelf -l    → shows the requested interpreter
```
