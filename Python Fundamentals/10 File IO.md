# File I/O

## Why File I/O?

Data stored only in memory disappears when the program exits. Files provide **persistent storage** that survives beyond a single run.

## `open()` and File Modes

```python
file = open("names.txt", "w")
file.write("David\n")
file.close()
```

| Mode | Meaning |
|---|---|
| `"r"` | Read |
| `"w"` | Write — **overwrites** the entire existing file |
| `"a"` | Append — adds to the end |
| `"x"` | Create a new file — fails if the file already exists |

`\n` (newline) matters when appending repeatedly, so entries don't run together on one line.

## `with` — Automatic File Closing

```python
with open("names.txt", "a") as file:
    file.write(f"{name}\n")
```
`with` automatically closes the file when the block ends — no need to remember `.close()` (and it still closes properly even if an exception occurs inside the block).

**Reading:**
```python
with open("names.txt", "r") as file:
    lines = file.readlines()      # all lines into a list

for line in lines:
    print("hello,", line.rstrip())    # rstrip() removes trailing whitespace/newline
```
A file object is itself iterable, so you can skip `readlines()` entirely:
```python
with open("names.txt") as file:
    for line in file:
        print("hello,", line.rstrip())
```

## CSV (Comma-Separated Values)

```text
name,home
Harry,"Number Four, Privet Drive"
Ron,The Burrow
```

⚠️ Manual splitting (`line.rstrip().split(",")`) is **fragile** — a value can itself contain a comma (as in the example above), breaking a naive split. Use the `csv` module instead.

### `csv.reader`
```python
import csv

with open("students.csv") as file:
    reader = csv.reader(file)
    for row in reader:
        print(row[0], row[1])     # row is a list
```

### `csv.DictReader`
```python
with open("students.csv") as file:
    reader = csv.DictReader(file)
    for row in reader:
        print(row["name"], row["home"])     # access by column name, not position
```

### Sorting with `key`
```python
for student in sorted(students, key=lambda student: student["name"]):
    print(student)
```
A `lambda` is an anonymous, one-line function — equivalent to writing a named function and passing it as `key=`.

### `csv.DictWriter`
```python
with open("students.csv", "a") as file:
    writer = csv.DictWriter(file, fieldnames=["name", "home"])
    writer.writerow({"name": name, "home": home})
```

## Binary Files and `PIL`

A **binary file** stores raw bytes — images, audio, video, etc. **PIL** (typically via the `Pillow` package) works with image files:
```python
import sys
from PIL import Image

images = [Image.open(arg) for arg in sys.argv[1:]]

images[0].save(
    "costumes.gif",
    save_all=True,
    append_images=[images[1]],
    duration=200,
    loop=0
)
```
This example builds an animated GIF from multiple loaded images.

## Cheat Sheet

```
open(path, mode)     "r" read  "w" write(overwrites)  "a" append  "x" create-new(fails if exists)
with open(...) as f:    auto-closes, even on exception

f.readlines()          all lines → list
for line in f: ...       file is directly iterable
line.rstrip()               strip trailing whitespace/newline

csv.reader        → rows as lists
csv.DictReader      → rows as dicts, by column name
csv.DictWriter        → write dicts as rows
sorted(x, key=lambda i: i["field"])    sort by a specific field

Binary file = raw bytes;  PIL/Pillow → Image.open() / .save()
```
