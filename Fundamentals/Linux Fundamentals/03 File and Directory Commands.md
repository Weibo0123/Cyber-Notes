# File & Directory Commands

## Listing — `ls`

```bash
ls              # current directory
ls /some/dir    # inspect without changing CWD
ls -a           # include hidden files (dotfiles)
ls -l           # detailed (permissions, owner, size, date)
ls -lah         # combine: detailed + hidden + human-readable sizes
```

**Hidden files / dotfiles** (`.bashrc`, `.config`) are simply excluded from normal `ls` output — hidden ≠ protected, encrypted, or inaccessible. Always check for dotfiles when enumerating an unfamiliar filesystem.

## Reading — `cat`

```bash
cat FILE           # print contents
cat FILE1 FILE2    # concatenate and print both
cat                # no file given → reads from stdin
```
This is your first exposure to Unix **standard streams** (`stdin`/`stdout`) — foundational for pipes, redirection, and syscalls later (file 06).

## Creating — `touch` and `mkdir`

```bash
touch FILE     # create empty file, or just update timestamps if it exists
mkdir DIR      # create a directory
```
⚠️ `touch` does **not** clear an existing file's contents. Output redirection (`> file`) *does* truncate it — don't confuse the two.

## Copying and Moving — `cp` and `mv`

```bash
cp SOURCE DEST     # copy; original remains
mv SOURCE DEST     # move/rename; original path no longer exists
```
If `DEST` is an existing directory, both commands place the result inside it, keeping (`cp`) or moving (`mv`) the source's name.

```
cp: A ──copy──→ B     (A still exists)
mv: A ──move──→ B     (A no longer exists at old path)
```

## Removing — `rm`

```bash
rm FILE
```
`rm` removes the file's directory entry (related to the `unlink()` syscall). This is different from truncating: `> file` empties a file's *contents* but keeps the file; `rm` removes the file itself.

## Searching the Filesystem — `find`

```bash
find                          # recursively search CWD (default location)
find /some/dir                # search starting there instead
find /some/dir -name NAME     # only entries matching NAME
find /                        # search the whole filesystem (slow, and likely to hit...)
```
Searching from `/` as a normal user will usually print permission errors for directories you can't enter — that's expected, not a failed search; it just means those subtrees were skipped.

`find` is your first real exposure to **recursive traversal** of the filesystem tree — the same idea reappears in `grep -r` and general filesystem enumeration.

## Searching Text — `grep`

```bash
grep "pattern" FILE     # print matching lines
```
`grep` filters whole **lines**, line by line, based on whether they match a pattern. (Options like `-i`, `-n`, `-r`, `-E`, and `-v` are covered in file 07 alongside other text tools.)

## Comparing Files — `diff`

```bash
diff FILE1 FILE2
```
Output uses `<` for lines from the first file, `>` for the second, with change markers like `2c2` (change), `a` (add), `d` (delete). Most useful when two files are nearly identical and you need to spot what changed — configs, forensic comparisons, CTF diffing.

## Links: Hard vs. Symbolic

```bash
ln -s TARGET LINK_NAME    # create a symlink: LINK_NAME → TARGET
```

```
Hard link:                          Symbolic link:
name A ──┐                          name A ──→ inode ──→ data
          ├──→ inode ──→ data       name B ──→ symlink ──→ target path
name B ──┘
```

| | Hard Link | Symbolic Link |
|---|---|---|
| Points to | Same inode directly | A path (may go stale) |
| Can point to a directory | Generally no | Yes |
| Can cross filesystems | Generally no | Yes |
| Can dangle (target removed) | No | Yes |

A symlink is **not** a copy — it's a pointer. If the target changes, accessing it through the symlink sees the new contents (unlike `cp`, which freezes a snapshot).

## Identifying File Types — `file`

```bash
file FILE
```
Reports what a path actually is, based on its **contents**, not its name/extension — `ASCII text`, `symbolic link to ...`, `ELF 64-bit LSB executable`, `directory`, etc. Useful when you can't assume a path is an ordinary file.

## Enumeration Mindset

When you land in an unfamiliar filesystem:
```
What exists?          → ls
Anything hidden?       → ls -a
Where is it?           → find
What's inside?         → cat / grep
Different from X?       → diff
What type is it?        → file
```

## Cheat Sheet

```
ls / ls -a / ls -l / ls -lah      list, hidden, detailed, combined
cat FILE                            print contents
touch FILE                          create empty / update timestamp
mkdir DIR                           create directory
cp SRC DEST                         copy (source remains)
mv SRC DEST                         move/rename (source gone)
rm FILE                             remove
find [LOC] [-name X]                recursive search
grep "pattern" FILE                 filter matching lines
diff FILE1 FILE2                    line-by-line comparison
ln -s TARGET LINK                   create symlink
file FILE                           identify file type by content

- = regular file    d = directory    p = FIFO/pipe   (more types exist, e.g. l = symlink)
```
