# Linux File System Hierarchy

File 01 covered how paths are written and resolved; this file covers what actually lives at the standard, well-known locations in that tree. In Linux, everything is represented as a file, and those files sit inside a single hierarchical structure rooted at `/`.

## Main Directories

| Directory | Purpose |
|---|---|
| `/bin` | Executable binaries for basic system commands |
| `/boot` | Files needed during startup and kernel loading |
| `/dev` | Device files representing hardware (e.g. `/dev/sda1` for a disk/partition) |
| `/etc` | System configuration files — including password-related files (file 15) |
| `/home` | Personal files/directories for regular users |
| `/lib` | Library files used by executable binaries |
| `/media` | Mount point for removable media (USB drives, CD-ROMs) |
| `/mnt` | Mount point for temporarily mounted filesystems |
| `/opt` | Additional, separately installed application software |
| `/proc` | Runtime info about currently running processes and system state (file 11) |
| `/root` | Home directory of the root user (not the same as `/` — see below) |
| `/run` | Runtime information since the last boot |
| `/sbin` | System binaries, traditionally for administrative use |
| `/srv` | Data provided by system services (e.g. FTP, TFTP) |
| `/tmp` | Temporary files, commonly cleared on reboot |
| `/usr` | User-accessible programs, libraries, and other files |
| `/var` | Variable data: logs, caches, and other data that changes during normal operation |

## `/` Is Not `/root`

This is worth stating explicitly, because the names look almost identical:

| Path | Meaning |
|---|---|
| `/` | The top-level root *directory* of the entire filesystem |
| `/root` | The *home directory* belonging to the root *user* |

File 01 already flagged that "root directory ≠ root user" — `/root` is exactly where that distinction becomes concrete: it's a specific directory that happens to belong to the root user, sitting a few levels down inside `/`, not the filesystem root itself.

## Directories Worth Remembering for Investigation

A few of these directories matter more than others once you're actually poking around a live system rather than just navigating it:

- **`/home`** often holds evidence of user activity — downloads, documents, shell history — since it's where regular users' own files accumulate.
- **`/tmp`** holds temporary files that commonly disappear on reboot, which makes its *current* contents relevant specifically because they might not persist.
- **`/var`** holds logs and caches that build up during normal operation — usually the first place to look for a record of what a system has been doing.
- **`/proc`** isn't made of ordinary persistent files at all — it's a live view into running processes and kernel state, generated on the fly rather than stored on disk.

## Related: File 01 for path syntax and navigation mechanics; file 11 for how `/proc` relates to viewing running processes; file 15 for what actually lives inside `/etc` regarding user accounts and passwords.

## Cheat Sheet

```
/        top-level root directory (≠ root user)
/root    root USER's home directory
/home    regular users' personal files
/etc     configuration files (passwd, shadow, etc.)
/tmp     temporary files, often cleared on reboot
/var     logs, caches, variable runtime data
/proc    live info on running processes/system state (not real files on disk)
/dev     device files (e.g. /dev/sda1)
/bin, /sbin, /usr, /lib   binaries and libraries
/boot    kernel/startup files
/media, /mnt   removable media / temporary mount points
/opt     extra, separately-installed software
/srv     data served by system services
```
