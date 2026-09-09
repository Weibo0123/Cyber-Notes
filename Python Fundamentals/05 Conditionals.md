# Conditionals

## Comparison Operators and Booleans

```
>   <   >=   <=   ==   !=
```
`=` assigns; `==` compares. Comparisons produce a **Boolean** (`True`/`False`).

## `if`, `elif`, `else`

```python
if x < y:
    print("x is less than y")
elif x > y:
    print("x is greater than y")
else:
    print("x is equal to y")
```
- `if` runs its block when the condition is `True`.
- `elif` ("else if") conditions are checked top-to-bottom — the **first** one that's `True` runs, and the rest are skipped.
- `else` is the default when nothing above matched.

⚠️ Standalone `if` statements are evaluated **independently**; an `if`/`elif`/`else` chain stops after the first match. Mixing these two patterns unintentionally (several separate `if`s when you meant one chain) is a common logic bug.

## `or` and `and`

```python
if x < y or x > y:        # True if AT LEAST ONE is True
    print("x is not equal to y")

if score >= 90 and score <= 100:    # True only if BOTH are True
    print("Grade: A")
```
Prefer the simplest expression that directly states the intent — `x != y` says the same thing as `x < y or x > y`, more clearly.

### Chained Comparisons
```python
if 90 <= score <= 100:
    print("Grade: A")
```
Conditions can also often be simplified by relying on the order of an `if`/`elif` chain instead of writing a compound condition:
```python
if score >= 90:
    print("Grade: A")
elif score >= 80:
    print("Grade: B")
elif score >= 70:
    print("Grade: C")
elif score >= 60:
    print("Grade: D")
else:
    print("Grade: F")
```

## Modulo for Even/Odd

```python
if x % 2 == 0:
    print("Even")
else:
    print("Odd")
```
`%` returns the remainder; a number is even exactly when its remainder after dividing by 2 is 0.

## Boolean-Returning Functions

```python
def is_even(n):
    return n % 2 == 0
```
Since `n % 2 == 0` already evaluates to `True`/`False`, there's no need for an `if`/`else` wrapper that just returns `True` or `False` explicitly — that's redundant.

## Pythonic Code

**Pythonic** code leans on the language's own idioms for clarity:
```python
return True if n % 2 == 0 else False    # conditional expression — still not ideal here
return n % 2 == 0                          # simplest, since the expression IS already a bool
```
Readability is a core part of writing Pythonic code, not just brevity for its own sake.

## `match`

Pattern-matches a value against `case`s:
```python
match name:
    case "Harry" | "Hermione" | "Ron":
        print("Gryffindor")
    case "Draco":
        print("Slytherin")
    case _:
        print("Who?")
```
- Cases are checked in order; the first match runs, then matching stops.
- `_` is a **wildcard**, acting like a default case.
- `|` matches multiple values in one case.

## Cheat Sheet

```
if cond:
elif cond:
else:
      → first True branch runs, top to bottom; else = default

and (both true)   or (at least one true)   not (invert)
chained comparison: a <= x <= b

% for even/odd:  n % 2 == 0
Boolean function: return the comparison directly, don't wrap it in if/else

match value:
    case "a" | "b":
    case _:            # wildcard/default
```
