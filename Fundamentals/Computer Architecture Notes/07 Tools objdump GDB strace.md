# Tools: objdump, GDB, strace

## Static vs Dynamic Analysis

| Static Analysis | Dynamic Analysis |
|---|---|
| Doesn't execute the program | Executes the program |
| Reads the code | Observes runtime behavior |
| `objdump` | `gdb`, `strace` |

---

## objdump — Static Disassembly

```bash
objdump -d -M intel <binary>
```
- `-d` → disassemble executable sections (mainly `.text`)
- `-M intel` → use Intel syntax (`mov rax, 0x3c`) instead of the default AT&T syntax (`mov $0x3c,%rax`). **Always use `-M intel`.**

### Reading the output

```
401015: 0f 05    syscall
```
| Part | Meaning |
|---|---|
| `401015` | Memory address |
| `0f 05` | Raw machine-code bytes |
| `syscall` | The assembly instruction |

**Tip:** watch for values loaded into a register and then overwritten before use — reverse-engineering challenges often hide the "real" value in an earlier, discarded instruction.

---

## GDB (GNU Debugger)

### Basic workflow

```gdb
gdb ./program        # load the binary (not running yet)
starti                # start at the very first instruction
disassemble           # (disas) view current function's assembly
stepi                 # (si) execute ONE machine instruction
print $rdi            # (p) show a register's value
x/x $rsp              # examine memory
set $rdi = 1337        # modify a register
info registers        # (i r) show all registers
```

### Running with arguments / stdin

```gdb
run hello world        # argv[1]="hello", argv[2]="world"
run < input.txt         # redirect stdin from a file
```
- `run <arg>` sets **argv**.
- `run < file` sets **stdin**. These are different — don't confuse them.

### x (examine memory) — format cheat sheet

```gdb
x/x  addr    # hex
x/d  addr    # decimal
x/s  addr    # string
x/i  addr    # instruction
x/b  addr    # byte
```
General form: `x/[count][format][size] address`

### Breakpoints & stack

```gdb
break function     # (b) set breakpoint
break *address      # break at raw address
continue            # (c) resume until next breakpoint
backtrace            # (bt) show call stack
frame N               # switch stack frame
```

### pwndbg extras

```gdb
context              # show full debugging context
vmmap                 # memory mappings
telescope $rsp         # view stack contents
```

### Common registers

```
$rip → next instruction    $rsp → stack pointer    $rbp → base pointer
$rax → return value / syscall #    $rdi/$rsi/$rdx → 1st/2nd/3rd argument
```

---

## strace — System Call Tracer (dynamic analysis)

Records every **system call** a running program makes — not CPU instructions, but interactions with the kernel (`read`, `write`, `open`, `execve`, `exit`, `alarm`, ...).

```bash
strace ./program
```

### Reading the output

```
write(1, "Hello\n", 6) = 6
alarm(10) = 0
exit(42)
+++ exited with 42 +++
```
Format: `syscall(arguments) = return_value`

- `execve(...)` is almost always the **first** line — Linux uses it to start the program.
- `exit(N)` → `N` is the process's exit code.
- `alarm(N)` → schedules a `SIGALRM` after `N` seconds (often used for timeouts).

Common task: use `strace` to find the exact argument passed to a specific syscall.

## Cheat Sheet

```
objdump -d -M intel <bin>   → static disassembly, Intel syntax
gdb ./bin → starti → disas → si → p $reg → x/x addr → set $reg=val
run arg      → sets argv
run < file    → sets stdin
strace ./bin   → trace every syscall the program makes
```
