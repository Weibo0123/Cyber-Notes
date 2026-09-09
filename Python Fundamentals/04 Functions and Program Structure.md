# Functions & Program Structure

## What Is a Function?

A **function** is a reusable operation that performs a specific task, called using parentheses:
```python
print("hello, world")
```
Values passed to a function are **arguments**; functions can accept multiple arguments, and a called function performs its computation, then execution returns to where it was called.

## Defining a Function

```python
def hello():
    print("hello")
```
Python uses `def`; the function **body must be indented** — indentation is what determines which statements belong to the function.

### Parameters
```python
def hello(to):
    print("hello,", to)

hello(name)     # `name`'s value is passed into the parameter `to`
```

### Default Parameters
```python
def hello(to="world"):
    print("hello,", to)

hello()        # hello, world  (default used since no argument given)
```

## Return Values

A function can send a value back to its caller with `return`:
```python
def square(n):
    return n * n

def main():
    x = int(input("What's x? "))
    print("x squared is", square(x))
```
The argument is passed in, the function computes, and the result is handed back to the caller — functions that `return` values (rather than just `print`-ing) are also far easier to test (file 13, Unit Tests).

## `main()`

Larger programs commonly organize their entry point in a `main()` function:
```python
def main():
    name = input("What's your name? ")
    hello(name)

def hello(to="world"):
    print("hello,", to)

main()
```
⚠️ **Defining a function does not execute it** — `main()` still has to be *called* explicitly at the bottom (or via the `if __name__ == "__main__":` guard, commonly seen when a file might also be imported elsewhere).

## Basic Numeric Sum Example

```python
def number_sum(number1, number2):
    total = number1 + number2
    return total

result = number_sum(10, 20)
print(result)   # 30
```

## Cheat Sheet

```
def name(params):
    ...body (indented)...
    return value          # optional; sends a value back to the caller

Defining a function ≠ running it — it must be called
Default parameter:  def f(x="default"): ...
Functions that return values (vs. only print) are easier to test/reuse

main() convention: define main() + helper functions, then call main() explicitly
```
