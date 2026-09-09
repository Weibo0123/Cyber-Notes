# Loops

## `while` Loops

Repeats a block as long as its condition is `True`, checked **before** each iteration:
```python
i = 0
while i < 3:
    print("meow")
    i += 1
```
Each execution of the block is an **iteration**. Make sure the condition can eventually become `False`, or the loop never ends.

## `for` Loops

Iterates over the elements of an **iterable** (list, string, `range`, ...):
```python
for i in range(3):        # 0, 1, 2
    print("meow")

for _ in range(3):          # use _ when the loop variable itself isn't needed
    print("meow")
```

### String Repetition
```python
print("meow" * 3)             # meowmeowmeow
print("meow\n" * 3, end="")     # \n = newline
```

## Loop Control: `break` and `continue`

| | Effect |
|---|---|
| `break` | exit the loop entirely, immediately |
| `continue` | skip the rest of this iteration, go to the next one |

```python
while True:
    n = int(input("What's n? "))
    if n < 0:
        continue
    break
```
`while True` loops forever unless something inside explicitly stops it (`break`, or `return` if inside a function):
```python
def get_number():
    while True:
        n = int(input("What's n? "))
        if n > 0:
            return n     # return also exits the enclosing loop
```

## Lists

An ordered collection, zero-indexed, in `[]`:
```python
students = ["Hermione", "Harry", "Ron"]
print(students[0])

for student in students:      # iterate directly over elements
    print(student)
```

### `len()`
```python
print(len(students))
for i in range(len(students)):     # index-based iteration when you need the index
    print(i + 1, students[i])
```

## Dictionaries

Key-value pairs, in `{}`:
```python
students = {
    "Hermione": "Gryffindor",
    "Harry": "Gryffindor",
}
print(students["Hermione"])
```
```python
for student in students:                     # iterating gives KEYS
    print(student, students[student], sep=", ")   # sep controls the separator between print() args
```

### Lists of Dictionaries
```python
students = [
    {"name": "Hermione", "house": "Gryffindor", "patronus": "Otter"},
    {"name": "Draco", "house": "Slytherin", "patronus": None},   # None = absence of a value
]
for student in students:
    print(student["name"], student["house"], student["patronus"], sep=", ")
```

## Nested Loops

```python
def print_square(size):
    for i in range(size):
        print_row(size)

def print_row(width):
    print("#" * width)
```
The inner loop runs completely for each single iteration of the outer loop — useful for anything multi-dimensional (rows/columns, grids). Splitting repeated logic into functions (`print_row`) keeps nested-loop code readable.

## Cheat Sheet

```
while cond: ...     checked before each iteration
for x in iterable: ...    direct iteration; use _ if x unused
range(n)                    0, 1, ..., n-1

break       → exit the loop entirely
continue      → skip to next iteration
return         → (inside a function) exits the function AND any enclosing loop

list[i]        zero-indexed;  len(list)
for k in dict:    iterates over KEYS;  dict[k] gets the value
dict.get() / d[k]     value lookup by key
sep="..."               print()'s argument separator

Nested loops: inner loop completes fully per outer iteration
```
