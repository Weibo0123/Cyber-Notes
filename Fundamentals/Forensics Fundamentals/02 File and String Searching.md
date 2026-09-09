# 02 File and String Searching

Whenever you're handed an unfamiliar file — forensics challenge or not — your first move should always be the same: **figure out what kind of file it actually is before deciding how to open it.** This chapter covers that "recon toolkit": `file`, `grep`, and `find`.

## Step 1: Identify the file type with `file`

As soon as you get a new file, run:

```
$ file filename
```

`file` inspects the file's actual content (not just its extension) and tells you whether it's plain text, some compressed format, an executable, an image format, and so on. For example, if the output is:

```
filename: ASCII text, with very long lines
```

that tells you it's plain text, but with unusually long lines — which usually means large blocks of encoded or concatenated data are stuffed inside, worth investigating further with `cat` or a targeted string search.

If `file` tells you it's an archive (e.g. `Zip archive data`), the natural next step is to extract it:

```
$ unzip filename.zip
```

**Make this a reflex:** run `file` on any new file before doing anything else. It saves a lot of time you'd otherwise waste guessing at the format.

## Step 2: Search file contents with `grep`

If `cat`-ing the file shows a big block of seemingly random text, manually scanning for a target string (like a specific flag format) doesn't scale. That's what `grep` is for:

```
$ grep 'target string' filename
```

Common variants include `egrep` (extended regex support) and `fgrep` (matches literal strings instead of regex, and is faster). `man grep`'s first line sums up its purpose: it prints lines that match a given pattern.

If you're new to `grep`/regex, the man page tends to be dense and technical. It's often faster to look up a beginner-friendly `grep` tutorial first, rather than starting with the man page.

The underlying idea generalizes well beyond CTFs: any time you're facing "a big pile of text with a target piece of information buried in it" — log files, config files, decompiled source, whatever — think about whether `grep` can locate it for you.

## Step 3: Search the directory tree with `find`

If the target isn't "a string inside a text file" but "a specific file somewhere in the filesystem," `find` is the right tool:

```
$ find . -name target_filename
```

This means: starting from the current directory (`.`), recursively search all subdirectories for a file with that exact name. It prints the full relative path to the match, e.g.:

```
./folderA/folderB/.hidden_folder/folderC/target_file.txt
```

Once you have the path, you can `cd` into it step by step, or just pass the full path directly to `cat` or any other tool — you don't actually have to descend into the directory manually.

### A few supporting shell habits

- **Tab completion:** typing a path prefix and pressing Tab auto-completes it, which cuts down on typos significantly. If pressing Tab once does nothing, that means multiple entries match the prefix and the shell can't disambiguate — type a bit more and try again, or press Tab twice to list all the candidates.
- **Hidden files:** in Linux, any file or directory starting with `.` is "hidden" and won't show up in a plain `ls` — you need the `-a` flag (e.g. `ls -al`) to see it. Hiding the answer file inside a hidden directory is a very common trick in forensics challenges.

## Related: Chapter 03 (Disk Forensics) uses a similar "layer by layer, then locate" mindset — but with dedicated forensics tools instead of ordinary shell commands.

---

## Self-Test

1. Why is it recommended to run `file` on an unfamiliar file before `cat`-ing it? What could go wrong if you just `cat` a binary file directly?
2. After `find . -name xxx` gives you a path, do you have to `cd` into each directory step by step to see the file's contents? What's a faster alternative?
3. If Tab-completion does nothing when you press it once, what does that tell you, and what should you do?

## Cheat Sheet

| Tool | Purpose | Basic usage |
|---|---|---|
| `file` | Identify a file's real type | `file filename` |
| `cat` | Print text content directly | `cat filename` |
| `grep` / `egrep` | Search file contents for a string/pattern | `grep 'pattern' filename` |
| `find` | Search the directory tree by filename | `find . -name filename` |
| `ls -al` | List directory contents including hidden files | `ls -al` |

**General triage order:** `file` to identify type → extract/convert if needed → `cat`/`grep` to search content, or `find` to locate the file itself.
