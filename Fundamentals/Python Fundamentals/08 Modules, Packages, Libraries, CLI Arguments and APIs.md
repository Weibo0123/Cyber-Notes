# Modules, Packages, Libraries, Command-Line Arguments & APIs

## Modules and Packages

A **module** is a Python file (`.py`) containing related code — functions, classes, etc. — that can be imported into other programs:
```python
import letsdefend
```

A **package** is a *directory* that groups related modules together:
```
my_package/
├── __init__.py
└── my_module.py
```
```python
from my_package import my_module
my_module.my_function()
```
`__init__.py` marks the directory as a package (can be empty).

## Libraries

A **library** is reusable code — yours or someone else's — organized into importable modules:
```python
import random
from random import choice     # import just one function/name
```

### `random`
```python
random.choice(["heads", "tails"])     # pick one item
random.randint(1, 10)                   # random int, a through b INCLUSIVE
random.shuffle(cards)                     # shuffles a list IN PLACE, returns nothing
```

### `statistics`
```python
import statistics
statistics.mean([100, 90])
```

## Command-Line Arguments

`sys.argv` holds the arguments a program was run with:
```bash
python name.py David
```
```python
sys.argv        # ["name.py", "David"]
sys.argv[0]       # program name
sys.argv[1]         # first actual argument (all args are strings)
```
```python
import sys

if len(sys.argv) < 2:
    sys.exit("Too few arguments")     # sys.exit() prints and terminates immediately
elif len(sys.argv) > 2:
    sys.exit("Too many arguments")

print("hello, my name is", sys.argv[1])
```
`sys.exit()` lets argument validation be separated cleanly from the main program logic.

## Slices

```python
sequence[start:end]     # start included, end EXCLUDED
sys.argv[1:]              # from index 1 to the end — skips the program name
```
```python
for arg in sys.argv[1:]:
    print("hello, my name is", arg)
```

## Third-Party Packages: PyPI and `pip`

A **package** in this sense is third-party code adding functionality, published to **PyPI (Python Package Index)** and installed via `pip`:
```bash
pip install cowsay
```
```python
import cowsay
cowsay.cow("hello")
```

## APIs and JSON

An **API** lets programs interact with another program/service. The `requests` package sends HTTP requests:
```python
import requests
response = requests.get("https://example.com")
```
Responses are frequently in **JSON**:
```python
import json

data = response.json()               # JSON → Python dict/list
print(json.dumps(data, indent=2))      # Python → readable JSON string

for result in data["results"]:           # accessing nested JSON: a list of dicts
    print(result["trackName"])
```

## Making Your Own Libraries

Any `.py` file of reusable functions can be imported like a library:
```python
# sayings.py
def hello(name):
    print(f"hello, {name}")

def goodbye(name):
    print(f"goodbye, {name}")
```
```python
import sayings
sayings.goodbye("David")

# or:
from sayings import goodbye
goodbye("David")
```

## Cheat Sheet

```
Module = a .py file        Package = a directory of modules (needs __init__.py)
import module / from package import module

import random          random.choice() / random.randint(a,b) / random.shuffle(x)  (in-place)
import statistics        statistics.mean([...])

sys.argv                    [program_name, arg1, arg2, ...]  — all strings
sys.exit("message")            print + terminate
sequence[start:end]              slice; end excluded; [1:] skips index 0

pip install name            PyPI package installer
requests.get(url).json()      HTTP request → parsed JSON (dict/list)
json.dumps(data, indent=2)      Python → formatted JSON string

Your own .py file with functions = your own importable module
```
