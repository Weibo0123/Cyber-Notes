# Exceptions & Debugging

## Exceptions and Errors

- **Syntax errors** — Python can't even understand the code.
- **Runtime errors (exceptions)** — the code is valid, but something goes wrong while running (unexpected behavior, invalid input).

```python
x = int(input("What's x? "))     # entering "cat" → ValueError: invalid literal for int() with base 10
```
`ValueError` — the value has the right general type but an inappropriate specific value. Other common exceptions:
```python
10 / 0        # ZeroDivisionError
print(a)       # NameError, if `a` was never defined
```

## `try` / `except`

```python
try:
    x = int(input("What's x? "))
except ValueError:
    print("x is not an integer")
```
Different exception types can be handled separately:
```python
try:
    print(a)
except NameError:
    a = 10
    print(a)
```
⚠️ Keep only the **smallest necessary amount of code** inside `try`. This matters more than it looks:
```python
try:
    x = int(input("What's x? "))
except ValueError:
    print("x is not an integer")

print(f"x is {x}")     # can raise NameError if the try block failed — x was never assigned!
```

## `else`

Runs only when `try` completes **without** an exception — keeps the success path visually separate from error handling:
```python
try:
    x = int(input("What's x? "))
except ValueError:
    print("x is not an integer")
else:
    print(f"x is {x}")
```
Common pattern — keep asking until valid input arrives:
```python
while True:
    try:
        x = int(input("What's x? "))
    except ValueError:
        print("x is not an integer")
    else:
        break
```

## Building a Reusable `get_int()`

```python
def get_int(prompt):
    while True:
        try:
            return int(input(prompt))
        except ValueError:
            pass
```
`return` inside the loop exits both the loop and the function at once. `pass` does **nothing** — here it means invalid input just silently loops again, with no error message.

## Cheat Sheet: Exception Handling

```
try:            code that might raise an exception
except X:         handle exception type X specifically
else:               runs ONLY if try succeeded (no exception)
pass                  do nothing (e.g. silently ignore an exception)

Keep try blocks MINIMAL — code after an unhandled failure may reference undefined variables
```

---

## Debugging with `pdb`

Python's built-in interactive debugger — pause execution and inspect the program's live state.

```python
import pdb

def function():
    a = 10
    b = 5
    c = a + b
    print(c)

if __name__ == "__main__":
    pdb.set_trace()      # execution pauses HERE
    function()
```

### Common `pdb` Commands

| Command | Effect |
|---|---|
| `l` | List the current source code |
| `n` | Execute the next line |
| `p <variable>` | Print a variable's current value |
| `q` | Quit the debugger |

```text
(Pdb) n
(Pdb) p a
10
(Pdb) p b
5
(Pdb) p c
15
(Pdb) q
```

`pdb` is especially useful when a bug is hard to reason about from reading the code alone — stepping through line by line and inspecting real values often reveals the problem faster than adding more `print()` statements.

## Cheat Sheet: `pdb`

```
import pdb
pdb.set_trace()      # sets a breakpoint; execution pauses here

l    list current code       n   next line
p x    print variable x        q    quit debugger
```
