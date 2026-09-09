# Forensics & Incident Response with Python

Python serves as an **automation layer** around forensic/IR work — not a replacement for dedicated forensic tools, but a way to connect, automate, and customize workflows so large amounts of evidence can be processed efficiently. Common applications: IOC searching, file-change monitoring (file 05), hash-based file search, memory analysis, disk analysis, and combining evidence across systems.

## IOC Search

An **Indicator of Compromise (IOC)** is an artifact suggesting malicious activity: IP addresses, file hashes, domains, file paths, suspicious strings.

### Searching Linux Logs for an IP

```python
import os

def search_logs_for_ip(ip):
    log_directory = "/var/log"

    files = [
        os.path.join(dp, f)
        for dp, dn, filenames in os.walk(log_directory)
        for f in filenames
        if not f.endswith(".gz")
    ]

    for file in files:
        try:
            with open(file, "r", errors="ignore") as f:
                for line in f.readlines():
                    if ip in line:
                        print(f"[{file}] {line.strip()}")
        except Exception as e:
            print(f"Error in {file} file: {e}")

if __name__ == "__main__":
    target_ip = input("Input IP Address: ")
    search_logs_for_ip(target_ip)
```
| Piece | Purpose |
|---|---|
| `os.walk(log_directory)` | Recursively traverses a directory and all subdirectories |
| `.endswith(".gz")` filter | Skips compressed logs, which can't be read as plain text |
| `errors="ignore"` | Prevents a decoding error in one file from crashing the whole search |
| `if ip in line` | Simple substring search per line |

The target directory is easily swapped for an application-specific log path, e.g. `/opt/custom_web_app/logs`.

### Searching Windows Event Logs

Windows doesn't use `/var/log`-style files — it stores events in **Event Logs**, accessed via `pywin32`:
```python
import win32evtlog
import win32evtlogutil

def search_event_logs_for_ip(target_ip):
    logs = ["Application", "Security", "System"]

    for log in logs:
        try:
            hand = win32evtlog.OpenEventLog(None, log)
            flags = win32evtlog.EVENTLOG_BACKWARDS_READ | win32evtlog.EVENTLOG_SEQUENTIAL_READ

            while True:
                events = win32evtlog.ReadEventLog(hand, flags, 0)
                if not events:
                    break
                for event in events:
                    data = win32evtlogutil.SafeFormatMessage(event, log)
                    if target_ip in data:
                        print(f"[{log}] {data}")
        except Exception as e:
            print(f"Error in {log} log: {e}")
```
**Key difference:** the underlying *investigation logic* is identical (target IOC → search evidence → find matches → report) — only the evidence source and API differ by OS.

## Searching Files by SHA-256 Hash

Finding a file that matches a known-bad hash:
```python
import os
import hashlib

def calculate_sha256(file_path):
    sha256_hash = hashlib.sha256()
    with open(file_path, "rb") as f:
        for byte_block in iter(lambda: f.read(4096), b""):
            sha256_hash.update(byte_block)
    return sha256_hash.hexdigest()

def search_files_for_hash(target_hash):
    directory = "/usr/bin"     # Windows equivalent: "C:\\Windows\\System32"

    files = [
        os.path.join(directory, f)
        for f in os.listdir(directory)
        if os.path.isfile(os.path.join(directory, f))
    ]

    for file in files:
        if calculate_sha256(file) == target_hash:
            print(f"Matched: {file} - {calculate_sha256(file)}")

if __name__ == "__main__":
    target_hash = input("Input SHA-256 hash value: ")
    search_files_for_hash(target_hash)
```

### Hashing in Chunks — Why It Matters

```python
for byte_block in iter(lambda: f.read(4096), b""):
    sha256_hash.update(byte_block)
```
Reads and hashes the file **4096 bytes at a time**, instead of `f.read()`-ing the whole file into memory at once — this is what keeps hashing scalable to large files (see file 05's FIM example, which uses the simpler whole-file approach and would benefit from this same pattern for anything beyond small config files).

The hashing logic itself is cross-platform; only the target directory changes between Linux and Windows.

## Analyzing Memory

Python can automate **memory forensics** by driving external tools like **Volatility** (itself written in Python).

```
1. Acquire a memory dump
2. Analyze it with a forensic framework (e.g. Volatility)
3. Search for relevant evidence
```

### Creating a Linux Memory Dump with LiME
```bash
git clone https://github.com/504ensicsLabs/LiME
cd LiME/src
make
sudo insmod lime-$(uname -r).ko "path=/tmp/memdump.lime format=lime"
```
Produces `/tmp/memdump.lime` for later analysis.

### Automating Analysis with `subprocess`
```python
import subprocess
import os

def search_memory_with_volatility(memdump, keyword):
    command = [
        "volatility", "-f", memdump,
        "--profile=LinuxUbuntu_x64",
        "linux_find_file", "-F", keyword, "-O", "output_dir"
    ]

    process = subprocess.Popen(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    stdout, stderr = process.communicate()

    if process.returncode == 0:
        print(stdout.decode())
    else:
        print(f"Error: {stderr.decode()}")

if __name__ == "__main__":
    memdump_path = "/tmp/memdump.lime"
    search_keyword = input("Search keyword: ")
    search_memory_with_volatility(memdump_path, search_keyword)
    os.remove(memdump_path)
```
| `subprocess` piece | Role |
|---|---|
| `Popen(command, ...)` | Launches the external process (here, `volatility`) |
| `stdout=PIPE`, `stderr=PIPE` | Captures its output instead of printing directly to the terminal |
| `communicate()` | Waits for completion, retrieves captured output |
| `returncode` | `0` typically means success — check it before trusting `stdout` |

```
Memory acquisition → memory dump → Python automation → Volatility analysis → findings
```
This pattern (drive an external forensic tool via `subprocess`, parse/relay its output) generalizes well beyond just Volatility — the same shape applies to automating other CLI forensic tools too. Memory dumps from multiple machines can be run through the same automated workflow.

## Disk Images

Python can inspect disk images via **`pytsk3`**, a Python interface to The Sleuth Kit, supporting many common filesystems:
```python
import pytsk3

def list_deleted_files(image_path):
    img_info = pytsk3.Img_Info(image_path)
    fs_info = pytsk3.FS_Info(img_info)
    file_list = fs_info.open_dir(path="/")

    print("Deleted Files:")
    for file in file_list:
        if "$" not in file.info.name.name.decode("utf-8"):
            try:
                file.read_random(0, 1)
            except IOError:
                print(file.info.name.name.decode("utf-8"))

image_path = "/mnt/images/ntfs.img"
list_deleted_files(image_path)
```
| Piece | Role |
|---|---|
| `pytsk3.Img_Info()` | Opens the disk image |
| `pytsk3.FS_Info()` | Provides access to the filesystem inside it |
| `open_dir(path="/")` | Opens the root directory |
| `read_random(0, 1)` | Attempts to read file data; an `IOError` here is used as a signal the file can't be read normally |

> This is a simplified demonstration of Python-based filesystem inspection, not a complete deleted-file recovery tool.

## Python's Role, Summarized

| Task | Python capability |
|---|---|
| IOC searching | Log/file search |
| Log analysis | `os`, file handling, string search |
| Hash analysis | `hashlib` |
| Memory analysis | `subprocess` + external tools (e.g. Volatility) |
| Disk analysis | `pytsk3` |
| Windows Event Logs | `pywin32` |
| Automation | Functions, loops, `subprocess` |

## Cheat Sheet

```
os.walk(dir)                  recursive directory traversal
errors="ignore"                  tolerate undecodable bytes when reading text
if ip in line                      simple substring IOC match

hashlib.sha256() + iter(lambda: f.read(4096), b"")     chunked hashing — use for large files
pywin32 (win32evtlog)                                     Windows Event Log access

subprocess.Popen(cmd, stdout=PIPE, stderr=PIPE) → communicate() → returncode
                                                            drive external forensic tools (Volatility, etc.)

pytsk3.Img_Info() / FS_Info() / open_dir()                inspect disk images (The Sleuth Kit)

Python's role: automation layer around dedicated forensic tools, not a replacement for them
```
