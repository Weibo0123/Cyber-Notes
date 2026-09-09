# Et Cetera: Advanced Python Idioms

## Global Variables

A **global variable** is defined outside any function and readable throughout the program — but a function can only *read* it directly; assigning inside a function creates a new **local** variable by default:
```python
balance = 0

def deposit(n):
    global balance     # required to actually modify the global, not shadow it
    balance += n
```
⚠️ Use global variables sparingly. A class with instance attributes is usually a cleaner alternative:
```python
class Account:
    def __init__(self):
        self._balance = 0
```

## Constants

Python has no true enforced constants — by convention, use **uppercase names**:
```python
MEOWS = 3
```
Usually defined near the top of a file; class-level constants are accessed via the class: `Cat.MEOWS`.

## Type Hints

Annotate expected types — purely informational, **not enforced at runtime**:
```python
def meow(n: int) -> str:
    return "meow\n" * n

number: int = 10
```
`-> None` means the function returns nothing. `mypy` can statically check code against its type hints:
```bash
pip install mypy
mypy meows.py
```

## Docstrings

Document what a function/class/module does, as a string right inside the definition:
```python
def meow(n):
    """Meow n times."""
    return "meow\n" * n
```
Tools like Sphinx can generate documentation directly from docstrings.

## `argparse`

A cleaner alternative to manually parsing `sys.argv` (file 08):
```python
import argparse

parser = argparse.ArgumentParser(description="Meow like a cat")
parser.add_argument("-n", default=1, help="number of times to meow", type=int)
args = parser.parse_args()

for _ in range(args.n):
    print("meow")
```
`add_argument()` declares an expected flag/argument (with `default`, `help` text, and `type` conversion built in); `-h`/`--help` is generated automatically.

## Unpacking

```python
first, _ = name.split(" ")     # assign elements of a sequence to multiple names at once
```

**`*` unpacking** — spreads a sequence into positional arguments:
```python
coins = [100, 50, 25]
total(*coins)          # same as total(100, 50, 25)
```
**`**` unpacking** — spreads a dict into keyword arguments:
```python
coins = {"galleons": 100, "sickles": 50, "knuts": 25}
total(**coins)           # same as total(galleons=100, sickles=50, knuts=25)
```

## `*args` and `**kwargs`

The reverse direction — **collecting** an arbitrary number of arguments:
```python
def f(*args):        # collects positional args into a tuple
    print(args)

def f(**kwargs):        # collects keyword args into a dict
    print(kwargs)

def f(*args, **kwargs):   # both together
    ...
```

## `map` and `filter`

```python
uppercased = map(str.upper, words)         # apply a function to every element
```
```python
def is_gryffindor(student):
    return student["house"] == "Gryffindor"

gryffindors = filter(is_gryffindor, students)      # keep elements where the function is True
# equivalently, with a lambda:
gryffindors = filter(lambda s: s["house"] == "Gryffindor", students)
```
Both return an iterable object (a `map`/`filter` object), not a list — wrap in `list(...)` if you need one. A list comprehension (file 07) is often a more Pythonic equivalent to `map`/`filter` + `lambda`.

## `enumerate`

Provides both index and value while iterating, instead of manually indexing with `range(len(...))`:
```python
students = ["Hermione", "Harry", "Ron"]
for i, student in enumerate(students):
    print(i + 1, student)
```

## Generators, `yield`, and Iterators

Returning a huge list all at once can be memory-expensive, since the whole result has to be built first. A **generator** produces values one at a time instead, using `yield` in place of `return`:
```python
def sheep(n):
    for i in range(n):
        yield "🐑" * i

for s in sheep(1000000):
    print(s)
```
`yield` produces one value and **pauses** the function until the next value is requested — the function's state is preserved between calls. This lets generators process arbitrarily large sequences without holding the entire sequence in memory at once. An **iterator** is, generally, any object capable of producing values one at a time during iteration (a generator is one specific kind of iterator).

## Cheat Sheet

```
global x        (inside a function) — required to modify a global, not just read it
UPPERCASE = value      convention for constants (not enforced)
def f(n: int) -> str:      type hints — informational only; check with mypy
"""docstring"""              documents a function/class/module

argparse: ArgumentParser() → add_argument() → parse_args()

*seq     unpack sequence into positional args      **dict   unpack dict into keyword args
*args      COLLECT positional args into a tuple      **kwargs  COLLECT keyword args into a dict

map(fn, iterable)      filter(fn, iterable)      — return iterables, not lists
enumerate(iterable)       gives (index, value) pairs

yield        pauses & produces one value at a time (generator)
Generator     memory-efficient, lazy, one-shot iterable
Iterator        general term: anything producing values one at a time
```
