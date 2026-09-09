# Signed vs Unsigned & Endianness

## Signed vs Unsigned

**Core idea: data itself is neither signed nor unsigned — the CPU only stores bits.**

```
11111111
```
This bit pattern doesn't say "I'm signed" or "I'm unsigned." Interpretation depends on how the *program* uses it.

Example (8-bit `11111111`):
- As **unsigned**: `255`
- As **signed** (two's complement): `-1`

The bits are identical — only the interpretation changes.

### Who decides the interpretation?
1. The language type (`int x;` vs `unsigned int y;`)
2. The compiler (generates different instructions)
3. The CPU instruction actually used

### Comparison instructions

| Signed | Meaning               | Unsigned | Meaning             |
| ------ | --------------------- | -------- | ------------------- |
| `jg`   | Jump if Greater       | `ja`     | Jump if Above       |
| `jge`  | Jump if Greater/Equal | `jae`    | Jump if Above/Equal |
| `jl`   | Jump if Less          | `jb`     | Jump if Below       |
| `jle`  | Jump if Less/Equal    | `jbe`    | Jump if Below/Equal |

Example: `EAX = 0xFFFFFFFF`
- Unsigned interpretation: `4294967295`
- Signed interpretation: `-1`

```asm
cmp eax, 1
ja target   ; 4294967295 > 1 → JUMP (unsigned)

cmp eax, 1
jg target   ; -1 > 1 → NO JUMP (signed)
```
Same register, same bits — different comparison result depending on the instruction.

### Sign / zero extension

```asm
movsx eax, byte ptr [...]   ; sign-extend (preserves sign, fills with 1s or 0s)
movzx eax, byte ptr [...]   ; zero-extend (fills upper bits with 0)
```

### Reverse-engineering rule of thumb

Never ask "is this value signed?" Ask: **"How is the program interpreting these bits here?"**
Look for: decompiler-recovered types, `jg`/`jl` vs `ja`/`jb`, `movsx` vs `movzx`.

---

## Little Endian (x86-64)

**Endianness** = the order multi-byte values are stored in memory.

Value `0x12345678` splits into bytes `12 34 56 78`. Question: which byte goes at the *lowest* address?

- **Big Endian** — most significant byte first: `12 34 56 78`
- **Little Endian** (x86-64 uses this) — least significant byte first: `78 56 34 12`

```
Value: 0x12345678
Little-endian memory:
0x1000 → 78
0x1001 → 56
0x1002 → 34
0x1003 → 12
```

**Only byte order flips — bits inside each byte stay the same.** `0x78` is still `01111000`, not reversed.

### Why it matters for pwn

Writing an address like `0x401234` (8-byte, x86-64) must be written in little-endian order:

```python
p64(0x401234)  # → 34 12 40 00 00 00 00 00
```

### Common mistake
Little-endian does **not** mean reversing the whole number (`0x12345678` ≠ `0x87654321`). It only reverses the *byte order* in memory, not the digits.

## Cheat Sheet

```
Endianness      = byte order in memory
Little Endian   = lowest byte at lowest address (x86-64 default)
Big Endian      = highest byte first
p64()           = converts a value to little-endian bytes
```
