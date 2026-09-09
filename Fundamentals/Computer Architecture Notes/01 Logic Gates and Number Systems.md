# Logic Gates & Number Systems

## Logic Gates

| Gate | Rule |
|---|---|
| AND | Output 1 only if **both** inputs are 1 |
| OR | Output 1 if **at least one** input is 1 |
| NOT | Flips the input (0→1, 1→0) |
| XOR | Output 1 if inputs are **different** |
| NAND | NOT(AND) |
| NOR | NOT(OR) |
| XNOR | NOT(XOR) — output 1 if inputs are the **same** |

Logic gates are the building blocks of adders, registers, and CPUs — billions of transistors wired into gates.

---

## Binary (Base-2)

Only digits `0` and `1`. Everything in a computer — code, memory, images, text — ends up as binary.

Binary gains a digit at powers of two: `2, 4, 8, 16, 32, 64, 128...`

```
3  = 11
4  = 100
5  = 101   (4 + 1)
```

## Hexadecimal (Base-16)

Digits: `0-9, A(10), B(11), C(12), D(13), E(14), F(15)`

**Key relationship:** `1 hex digit = 4 bits` → `2 hex digits = 1 byte = 8 bits`

```
Binary → Hex: split into groups of 4 bits
11010110 → 1101 0110 → D6 → 0xD6

Hex → Binary: expand each digit to 4 bits
0xAF → A=1010, F=1111 → 10101111
```

Common values to recognize instantly:

```
0x00 = 00000000
0xFF = 11111111
0x7F = 01111111
0x80 = 10000000
```

## ASCII

| Char | Hex |
|---|---|
| `A`–`Z` | `0x41`–`0x5A` |
| `a`–`z` | `0x61`–`0x7A` |
| `0`–`9` | `0x30`–`0x39` |
| Space | `0x20` |

Strings are stored as consecutive ASCII bytes:
```
"ABC"   → 41 42 43
"Hello" → 48 65 6C 6C 6F
```

## Python Conversions

```python
0b1010        # binary literal → 10
0x41          # hex literal    → 65

bin(10)       # → '0b1010'
hex(65)       # → '0x41'
int("1010", 2)  # binary → decimal → 10
int("41", 16)   # hex → decimal    → 65
```

## Reverse-Engineering Mindset

When you see a byte value, think through all its possible meanings at once:

```
0x41  → hex number / decimal 65 / ASCII 'A' / binary 01000001
41 41 41 41 → "AAAA"
0x7fffffffe2a0 → looks like a memory address (long, starts with 0x7f...)
```

## Cheat Sheet

```
CPU understands binary.  
Humans read hex.  
Text is ASCII/UTF-8.
1 hex digit = 4 bits.    
2 hex digits = 1 byte.
```
