# File Integrity Monitoring (FIM) with Python

## What Is FIM?

**File Integrity Monitoring** watches specified files and detects whether their contents have changed:
```
1. Calculate an initial hash for each monitored file
2. Periodically recalculate each file's current hash
3. Compare current vs. original
4. Alert if a file has been modified or deleted
```
**Core idea:** if a file's contents change, its cryptographic hash will (for all practical purposes) change too. Comparing against a trusted **baseline hash** is a simple, reliable way to detect modification.

## Required Libraries

```python
import hashlib     # calculate cryptographic hashes
import time          # control the monitoring interval
import os              # check whether a file still exists
```

## Basic FIM Implementation

```python
import hashlib
import time
import os

files_to_monitor = ["/etc/passwd", "/etc/shadow"]
hash_dict = {}


def get_file_hash(file_path):
    with open(file_path, "rb") as f:
        return hashlib.sha256(f.read()).hexdigest()


def initialize_hashes():
    for file in files_to_monitor:
        hash_dict[file] = get_file_hash(file)


def check_integrity():
    for file, initial_hash in hash_dict.items():
        if not os.path.exists(file):
            print(f"{file} not found!")
            continue

        current_hash = get_file_hash(file)

        if current_hash != initial_hash:
            print(f"A change has been detected in {file}!")


initialize_hashes()

while True:
    check_integrity()
    time.sleep(3600)
```

## How It Works

| Step | What it does |
|---|---|
| `files_to_monitor` / `hash_dict` | List of watched paths; dict mapping each to its baseline hash |
| `get_file_hash()` | Opens the file **in binary mode (`"rb"`)**, hashes its full contents with SHA-256, returns hex |
| `initialize_hashes()` | Runs once at startup, establishing the baseline |
| `check_integrity()` | For each file: confirm it still exists, rehash it, compare to baseline |
| `while True` + `time.sleep(3600)` | Repeats the check every hour, indefinitely |

⚠️ `get_file_hash()` reads the **entire file into memory at once** (`f.read()`). This is fine for something like `/etc/passwd`, but doesn't scale to large files — file 06's hash-search example reads in **4096-byte chunks** instead specifically to avoid this; the same chunked pattern could be applied here for monitoring larger files.

### Monitoring Flow

```
Start → calculate initial hashes → check integrity → compare
  same       → wait 1 hour → check again
  different  → ALERT → wait 1 hour → check again
```

## Cheat Sheet

```
hashlib.sha256(f.read()).hexdigest()      hash a file's full contents
os.path.exists(file)                        confirm a monitored file is still present
time.sleep(3600)                              wait 1 hour between checks
while True: check_integrity()                    continuous monitoring loop

FIM logic:  baseline hash  vs.  current hash  →  match = unchanged, mismatch = alert
⚠️ For large files, hash in chunks (see file 06) instead of f.read() all at once
```
