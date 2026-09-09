# Computer Architecture / x86-64 / Pwn — Review Set

| #   | File                                                                                   | Covers                                                              |
| --- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------- |
| 01  | [Logic Gates and Number Systems](01%20Logic%20Gates%20and%20Number%20Systems.md)       | Logic gates, binary/hex/ASCII, Python conversions                   |
| 02  | [Signed Unsigned and Endianness](02%20Signed%20Unsigned%20and%20Endianness.md)         | Signed vs unsigned interpretation, little-endian                    |
| 03  | [Pointers and Memory Addressing](03%20Pointers%20and%20Memory%20Addressing.md)         | Pointers, dereferencing, partial registers, RIP-relative addressing |
| 04  | [The Stack](04%20The%20Stack.md)                                                       | rsp, push/pop, stack frames, call/ret, stack-as-array               |
| 05  | [Function Calls and Control Flow](05%20Function%20Calls%20and%20Control%20Flow.md)     | Calling convention, cmp/jump, loops                                 |
| 06  | [Data Access and Structures](06%20Data%20Access%20and%20Structures.md)                 | .data/.rodata/.bss/heap access, reconstructing structs              |
| 07  | [Tools objdump GDB and strace](07%20Tools%20objdump%20GDB%20strace.md)                 | Static & dynamic analysis tooling                                   |
| 08  | [ELF and Process Loading](08%20ELF%20and%20Process%20Loading.md)                       | fork/execve, ELF sections, dynamic linking, PIE/ASLR                |
| 09  | [Process Startup and Initial Stack](09%20Process%20Startup%20and%20Initial%20Stack.md) | argc/argv/envp layout, env -i, GDB address shifts                   |
| 10  | [Linux Signals](10%20Linux%20Signals.md)                                               | Signal numbers, handlers, SIGSEGV/SIGKILL/SIGSTOP                   |
| 11  | [Syscalls in Practice](11%20Syscalls%20in%20Practice.md)                               | read/write/open/exit assembly implementations                       |
| 12  | [Buffer Overflow and EIP Control](12%20Buffer%20Overflow%20and%20EIP%20Control.md)     | Offset-finding, NOP sleds, payload construction                     |