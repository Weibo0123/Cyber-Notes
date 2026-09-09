# Stack Buffer Overflow & Taking Control of EIP

*(32-bit examples — `EIP` is 4 bytes; on 64-bit the equivalent is `RIP`, 8 bytes)*

## The Goal

If a buffer overflow overwrites the saved **return address**, then when the function executes `ret`, the CPU loads your overwritten value into `EIP` — **you control where execution jumps next**.

```
Buffer overflow → overwrite return address → ret → EIP controlled → control execution flow
```

## Step 1 — Confirm EIP Is Reachable

Send a large amount of one repeated byte:
```python
'\x55' * 1200      # 0x55 = ASCII 'U'
```
If GDB shows `eip = 0x55555555` after the crash, your input reached and overwrote the saved return address.

## Step 2 — Find the Exact Offset

The **offset** = exact number of bytes from the start of input to the saved return address. It is **not simply "the buffer size"** — the real offset includes local variables, saved registers, padding, alignment, and saved `EBP`, so it must be found experimentally, not assumed.

### Cyclic pattern method

Instead of one repeated byte, use a **unique, non-repeating pattern** (`Aa0Aa1Aa2Aa3...`) so any 4-byte chunk tells you exactly where it came from.

```bash
pattern_create.rb -l 1200          # generate the pattern
# ... run the program with it, check GDB: eip = 0x69423569
pattern_offset.rb -q 0x69423569     # → "Exact match at offset 1036"
```
Result: `Offset = 1036 bytes`.

## Step 3 — Verify Control

```python
'\x55' * 1036 + '\x66' * 4
```
If the offset is right, GDB should show `eip = 0x66666666` — confirming those exact 4 bytes land on EIP.

```
32-bit → EIP = 4 bytes      64-bit → RIP = 8 bytes
```

## Step 4 — Plan the Full Payload

Once EIP is confirmed at offset 1036 (total 1040 bytes to reach + overwrite EIP):

```
┌──────────────┬──────────┬──────────────┬──────────┐
│    Buffer    │   NOPs   │  Shellcode   │   EIP    │
│   786 bytes  │ 100 bytes│  150 bytes   │  4 bytes │
└──────────────┴──────────┴──────────────┴──────────┘
Total = 786 + 100 + 150 + 4 = 1040
```

### Sizing the shellcode space

Check real shellcode size first, e.g.:
```
msfvenom -p linux/x86/shell_reverse_tcp ...   → Payload size: 68 bytes
```
Reserve more than the minimum (here, 150 bytes) as a safety margin for later changes.

### NOP sled — why use it

`\x90` = the x86 **NOP** (No Operation) instruction — CPU does nothing and moves on. A run of NOPs before the real shellcode gives the jump target tolerance for imprecise addresses:
```
NOP → NOP → NOP → NOP → SHELLCODE
```
If execution lands *anywhere* in the NOP region, it simply "slides" forward until it reaches the real shellcode.

### Placeholder bytes while testing

Before inserting real shellcode, use an obvious placeholder like `\x44` (`'D'`) just to confirm the 150-byte region fits and lands correctly — it's not functional code, just a marker.

### Computing the padding size

```python
Buffer = "\x55" * (1040 - 100 - 150 - 4)   # = 786 bytes
```

## Full Payload

```python
payload  = b"\x55" * 786    # Padding / buffer
payload += b"\x90" * 100    # NOP sled
payload += b"\x44" * 150    # Shellcode placeholder (real shellcode goes here later)
payload += b"\x66" * 4      # EIP overwrite
```

## Cheat Sheet

```
Goal        : overwrite the saved return address so ret jumps where WE want
Offset      : found experimentally (cyclic pattern), NOT assumed from buffer size
32-bit EIP  : 4 bytes        64-bit RIP: 8 bytes
NOP sled    : \x90 run before shellcode — gives landing tolerance
Payload     : [ padding ][ NOP sled ][ shellcode ][ EIP/RIP overwrite ]
Core idea   : find the offset first, THEN divide the remaining space between
              padding, a NOP sled, and the shellcode.
```
