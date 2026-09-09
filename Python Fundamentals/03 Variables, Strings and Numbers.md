# Variables, Strings & Numbers

## Running Python Code

Python source files use the `.py` extension:
```bash
python hello.py
```
Python interprets the source and executes the corresponding instructions.

## Bugs

A **bug** is an error or unintended behavior. **Syntax errors** occur when code doesn't follow Python's grammar; Python's error messages usually identify the type and location of the problem — read them, don't skip past them.

## Variables

A **variable** is a name that refers to a value, assigned with `=`:
```python
name = input("What's your name? ")
```
The right-hand expression is evaluated first; its result is then assigned to the left-hand name.

⚠️ A string in quotes is always literal text — `print("hello, name")` prints the literal word `name`, not whatever the variable `name` holds. To include a variable's value, you need concatenation or an f-string (below).

## Strings

A **string** (`str`) is a sequence of text.
```python
print("hello, " + name)             # concatenation
print("hello,", end="")             # print()'s `end` param defaults to "\n"
print(name)
print("hello, \"friend\"")           # \ escapes special characters, e.g. embedded quotes
```

### f-Strings
An **f-string** embeds expressions directly into a string — prefix the opening quote with `f`:
```python
print(f"hello, {name}")
```

### String Methods
```python
name = input("What's your name? ").strip().title()
```
- `.strip()` — removes leading/trailing whitespace
- `.title()` — converts to title case
- Methods chain left to right, and can be applied to the result of another operation.

## Integers

`int`, using the standard arithmetic operators (`+ - * /`, plus `%` for remainder).

### User Input and Type Conversion
`input()` **always returns a string**, so numeric input must be converted before arithmetic:
```python
x = int(input("What's x? "))
```
This is **casting** — converting a value from one type to another. Functions can nest (the inner one evaluates first): `int(input(...))` runs `input()`, then passes its result into `int()`.

## Readability

Multiple approaches can solve the same problem — prioritize **readability**: clear structure, meaningful names, and useful comments make code easier to maintain.

## Floating-Point Numbers

A **float** represents a real number with a fractional part:
```python
x = float(input("What's x? "))
```

### Rounding
```python
round(3.14159)          # nearest integer
round(3.14159, 2)         # optional 2nd arg: decimal places
```
Optional parameters are often shown in docs with square brackets: `round(number[, ndigits])`.

### Number Formatting with f-strings
```python
print(f"{z:,}")      # thousands separators
print(f"{z:.2f}")     # exactly 2 digits after the decimal point
```

## Comments and Pseudocode

```python
# Ask the user for their name
name = input("What's your name? ")

# Print the name
print(name)
```
**Pseudocode** describes a program's steps in human-readable form before (or alongside) the real implementation — useful for planning logic before worrying about syntax.

## Cheat Sheet

```
name = value                    assignment
"literal text"                    always literal — use f-string/concat for variables
f"hello, {name}"                   f-string: embed expressions directly
"a" + "b"                            concatenation
.strip()  .title()                    common string methods, chainable

int(input(...))     cast string input to int
float(input(...))     cast string input to float
round(x) / round(x, n)  round to nearest / n decimal places

f"{z:,}"     thousands separator      f"{z:.2f}"    2 decimal places

= assigns   ≠   ==  compares
```
