# Unit Tests

## What Is a Unit Test?

A test that checks a specific part of a program — usually a single function — rather than relying on manually eyeballing `print()` output. Testing multiple cases catches **edge/corner cases** that a couple of manual tries would likely miss.

## `assert`

```python
assert square(2) == 4
assert square(3) == 9
```
If the condition is true, execution continues silently; if false, Python raises `AssertionError`. This is shorter and more explicit than manually writing `if`/`print()` checks.

## `pytest`

A third-party testing framework:
```bash
pip install pytest
pytest test_calculator.py
```
Convention: put tests in a separate file, e.g. `test_calculator.py`:
```python
from calculator import square

def test_square():
    assert square(2) == 4
    assert square(3) == 9
```

### Organizing Tests into Functions
```python
def test_positive():
    assert square(2) == 4
    assert square(3) == 9

def test_negative():
    assert square(-2) == 4
    assert square(-3) == 9

def test_zero():
    assert square(0) == 0
```
`pytest` runs each test function independently — a failure in one doesn't prevent the others from running and reporting.

### Testing Exceptions
```python
import pytest

def test_str():
    with pytest.raises(TypeError):
        square("cat")
```
This passes only if `square("cat")` actually raises `TypeError`.

## Testing Functions That Return Values

Testing is much easier when a function **returns** its result instead of only printing it:
```python
def hello(to="world"):          # harder to test
    print("hello,", to)

def hello(to="world"):          # easy to test
    return f"hello, {to}"

def test_default():
    assert hello() == "hello, world"

def test_argument():
    assert hello("David") == "hello, David"
```

## Organizing Tests into Folders

```text
project/
├── hello.py
└── test/
    ├── __init__.py
    └── test_hello.py
```
`__init__.py` marks `test/` as a package; run everything at once with:
```bash
pytest test
```

## Cheat Sheet

```
assert condition              raises AssertionError if False

pip install pytest              pytest test_file.py    run tests
def test_x(): assert ...         one test function per case/group
pytest.raises(ExceptionType)       verify an exception is raised

Prefer return over print() in functions you intend to test
test/ folder + __init__.py         → pytest test  runs everything inside
```
