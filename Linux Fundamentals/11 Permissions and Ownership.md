# Permissions & Ownership

Linux access control for files rests on two ideas: **who owns it** (Ownership) and **what each category of user can do** (Permissions).

## Reading `ls -l`

```
-rw-r--r-- 1 alice students file.txt
```
```
-   rw-   r--   r--
│    │     │     └── Other
│    │     └──────── Group
│    └────────────── Owner
└─────────────────── File type
```
First character = file type: `-` regular file, `d` directory, `l` symlink, `p` FIFO (file 06), `c` character device. Owner/Group here: `Owner = alice`, `Group = students`. Ownership and permissions are independent — changing one doesn't change the other, and being owned by `root` does *not* by itself mean others are locked out (permission bits still decide that).

## The Three Basic Permissions — `rwx`

| Bit | On a regular file | On a directory |
|---|---|---|
| `r` | read contents | list directory entries |
| `w` | modify contents | create/delete/rename entries |
| `x` | execute the file | **traverse/enter** the directory |

⚠️ Directory `x` does **not** mean "run the directory" — it means a process can `cd` into it / reach objects inside it (subject to those objects' own permissions). Having `r` on a file doesn't imply `x` (readable ≠ executable), and vice versa — the three bits are independent.

## Groups

```bash
id        # shows uid, primary gid, and all groups you belong to
```
A user can belong to multiple groups; group membership is how multiple users can share one set of permissions on a resource.

## Changing Ownership and Permissions

```bash
chown USER FILE      # change owner
chgrp GROUP FILE      # change group
chmod MODE FILE        # change permission bits
```

### `chmod` symbolic mode: WHO + OP + WHAT

```
WHO:  u = owner   g = group   o = other   a = all (u+g+o)
OP:   +  add      -  remove      =  set exactly (clears anything unspecified)
WHAT: r  w  x
```
```bash
chmod u+x file        # add execute for owner
chmod g-w file          # remove write from group
chmod o=r file           # set other to exactly r--
chmod a-rwx file          # strip rwx from everyone
chmod u=rw,g=r,o=- file    # multiple changes, comma-separated
```
⚠️ **`-` vs `=` are different operations:** `chmod u-r file` removes just the `r` bit, leaving the rest of the owner's permissions alone. `chmod u=- file` clears the *entire* owner permission set. Don't conflate "remove one bit" with "reset to nothing."

## Special Permission Bits: SUID, SGID, Sticky

Beyond the 9 basic `rwx` bits (owner/group/other), Linux has three special bits:

### SUID — Set User ID
```
-rwsr-xr-x     ← 's' in the owner's execute slot
```
When an executable has SUID set, running it executes the process using the **file owner's** identity — not the identity of the user who launched it.
```bash
chmod u+s program
```
A SUID binary owned by `root` therefore lets any user who can execute it briefly run *as root* for that program's logic — powerful, and why SUID binaries must be carefully designed (see `su` in file 13 for a canonical example).

### SGID — Set Group ID
Same idea, using the **group** identity instead:
```bash
chmod g+s program
```

### Sticky Bit
```
drwxrwxrwt     ← 't' at the end
```
On a shared, world-writable directory (classic example: `/tmp`), the sticky bit stops users from deleting/renaming *other users'* entries just because they have directory write permission — each user can only remove their own.

## Cheat Sheet

```
ls -l                       show type + owner + group + permissions
id                            show uid / gid / groups

chown USER FILE                change owner
chgrp GROUP FILE                 change group

chmod u/g/o/a  +/-/=  r/w/x        symbolic mode
chmod u+s FILE                       SUID (owner identity on execution)
chmod g+s FILE                        SGID (group identity on execution)
chmod +t DIR                            sticky bit (restrict deletion in shared dirs)

rwx on file:  read / modify / execute
rwx on dir:    list / create-delete-entries / traverse-enter
```
