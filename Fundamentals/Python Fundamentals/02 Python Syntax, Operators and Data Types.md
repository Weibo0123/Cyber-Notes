# Python Syntax, Operators & Data Types

## What Is Python?

A high-level, general-purpose language known for readable syntax, created by **Guido van Rossum** and first released in **1991**. Widely used across AI/ML, web development, data analysis, automation, and more.

## Syntax Basics

- Statements normally end at the **end of the line** — no semicolons required.
- **Indentation** (convention: 4 spaces) defines code blocks (functions, conditionals, loops) instead of braces.
- Python is **case-sensitive**: `variable` and `Variable` are different names.
- Comments start with `#` and are ignored by the interpreter.

## Operators

### Arithmetic
| Operator | Name | Example |
|---|---|---|
| `+` `-` `*` `/` | add/subtract/multiply/divide | `x + y` |
| `%` | modulus (remainder) | `x % y` |
| `**` | exponentiation | `x ** y` |
| `//` | floor division | `x // y` |

### Assignment
```
=   +=   -=   *=   /=   %=   **=   //=
```
```python
x = 10
x += 5    # x = 15
```

### Comparison — return a Boolean
```
==   !=   >   <   >=   <=
```
⚠️ `=` assigns; `==` compares. Mixing these up is one of the most common early bugs.

### Logical
```
and     True only if BOTH conditions are true
or      True if AT LEAST ONE condition is true
not     reverses a Boolean
```

### Bitwise — operate on individual bits of integers
```
&  |  ^  ~  <<  >>
```

### Identity — same object, not just equal value
```python
a is b        # True only if a and b are the SAME object
a is not b
```
Different from `==`, which compares values, not object identity.

### Membership — test collection containment
```python
"x" in "example"
1 in [1, 2, 3]
```

## Built-in Data Type Categories

| Category | Types |
|---|---|
| Numeric | `int`, `float`, `complex` |
| Sequence | `str`, `list`, `tuple` |
| Mapping | `dict` |
| Set | `set`, `frozenset` |
| Boolean | `bool` |
| Binary | `bytes`, `bytearray`, `memoryview` |

You can also define **custom data types** using classes (file 12).

## Cheat Sheet

```
Indentation = block structure (4 spaces convention)
# comment

Arithmetic:  + - * / % ** //
Assignment:   = += -= *= /= %= **= //=
Comparison:    == != > < >= <=      (== compares, = assigns)
Logical:        and / or / not
Bitwise:          & | ^ ~ << >>
Identity:          is / is not      (same object, not just equal value)
Membership:         in / not in

Numeric: int float complex     Sequence: str list tuple
Mapping: dict                    Set: set frozenset
Boolean: bool                      Binary: bytes bytearray memoryview
```
