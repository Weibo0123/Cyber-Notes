# Users & Authentication

## UID, GID, and `root`

Linux identifies users and groups numerically:
```
Username → UID          Group name → GID
```
`root` is the superuser, always **UID 0**, with very broad system privileges. Regular users conventionally start at UID `1000`; service/system accounts (`www-data`, `mysql`, `sshd`, ...) commonly sit in the `1`–`999` range instead.

## Checking Who You Are: `whoami` and `id`

```bash
whoami      # prints just the current username, e.g. "debian"
id          # prints UID, GID, and every group the current user belongs to
id -u       # prints only the numeric UID
```
`id` is the more complete of the two — it's the quickest way to check both your own privilege level and your group memberships in one command, which matters directly for the group-based access questions covered in file 14.

## `/etc/passwd`

Basic account information, one line per user, 7 `:`-separated fields:
```
username:x:UID:GID:GECOS:home:shell
```
| Field | Meaning |
|---|---|
| 1 | Username |
| 2 | Password placeholder (`x` — actual hash lives in `/etc/shadow`) |
| 3 | UID |
| 4 | Default/primary GID |
| 5 | GECOS — user info/comment field |
| 6 | Home directory |
| 7 | Login shell |

```
hacker:x:1000:1000::/home/hacker:/bin/bash
root:x:0:0:...
```
System/service accounts also live here (`www-data`, `mysql`, `sshd`, `nobody`, ...) — not meant for interactive login. A shell of `/usr/sbin/nologin` signals exactly that: login is disabled for the account.

`/etc/passwd` is normally **world-readable** — that's precisely why passwords were moved out of it.

## `/etc/shadow`

Holds password authentication and lifecycle data instead:
```
username:password_field:other_fields...
```
Common values in the password field:
| Value | Meaning |
|---|---|
| `*` or `!` | password auth disabled for this account |
| *(empty)* | no password set — may allow passwordless login depending on config |
| `$6$salt$hash` | an actual password hash (`$6$` = SHA-512 crypt format) |

The remaining `:`-separated fields track the account's password *lifecycle*, not just its hash:

| Field | Meaning |
|---|---|
| 3 | Date of last password change (days since Jan 1, 1970) |
| 4 | Minimum password age — how long the user must wait before changing it again |
| 5 | Maximum password age — how long before the password must be changed |
| 6 | Warning period — how many days before expiration the user gets warned |
| 7 | Inactivity period — how long after expiration the account can still log in |
| 8 | Account expiration date |
| 9 | Reserved |

A `0` in field 3 (last-changed date) is a common convention meaning "the user must change their password at next login."

`/etc/shadow` is restricted to privileged users (normally just root) — unlike `/etc/passwd`, which anyone can read. A regular user trying to `cat /etc/shadow` directly will typically just get a permission error; reading it requires `sudo` (below) or an equivalent privilege escalation.

### Password Hashing

Linux never stores your plaintext password — only a one-way hash:
```
password → hash function → compare against stored hash → match = authenticated
```
The **salt** (`$6$salt$hash`) is random per-account data mixed into the hash, so identical passwords don't produce identical stored hashes.

**`john` (John the Ripper)** — a password-hash cracking tool. It does not "decrypt" a hash; it hashes *candidate* passwords and checks each one against the stored hash for a match:
```bash
john shadow_file
john --show shadow_file
```

## `su` — Substitute User

```bash
su username     # switch to a specific user (prompts for THEIR password)
su               # no argument → defaults to root
```
`/usr/bin/su` is itself a classic **SUID root** binary (file 13) — its permissions look like `-rwsr-xr-x`, owned by `root`. That SUID bit is *how* `su` is able to hand you another user's identity in the first place: it runs with root's effective privileges regardless of who launched it, then switches to the target user after successful authentication.

## `sudo` — Run One Command with Elevated Privileges

```bash
sudo whoami      # runs just this command as root; doesn't start a root shell
```
Unlike `su` (switches your whole session to another user, authenticated against *their* credentials), `sudo` authorizes *your* account to run a specific command with elevated privileges, governed by policy in `/etc/sudoers`.

| | `su` | `sudo` |
|---|---|---|
| Model | Switch to another user's session | Run one command elevated |
| Authenticates against | The **target** user's credentials | The **current** user, checked against policy |
| Policy source | — | `/etc/sudoers` |
| Default target | root | root (typically) |

Mental shortcut: `su` = "**s**witch **u**ser," `sudo` = "**do** this (as another privilege level)."

## Managing Accounts: `useradd`, `userdel`, `passwd`

Creating, removing, and updating accounts is normally a root/`sudo` operation, since it edits `/etc/passwd` and `/etc/shadow` directly:

```bash
useradd tempuser        # create a new account
userdel tempuser        # delete an existing account
passwd tempuser          # set/change that account's password (prompts for the new password)
```

`passwd` with no username changes the *current* user's own password instead. None of these three commands touch the target account's home-directory files by themselves beyond what's needed to create the account — deleting a user with `userdel` doesn't automatically remove their `/home` directory unless you pass an extra flag (`-r`) for that.

## Cheat Sheet

```
root = UID 0            regular users usually start at UID 1000

whoami                  print current username
id / id -u              print UID, GID, groups / just the UID

/etc/passwd     username:x:UID:GID:GECOS:home:shell   (world-readable, no real password)
/etc/shadow      username:hash:changed:min:max:warn:inactive:expire  (root-only, actual password data)

$6$salt$hash      SHA-512 crypt hash, with random salt

useradd user          create an account       userdel user       delete an account
passwd [user]          change a password (own account if no username given)

su [user]           switch user (default: root); authenticates as the TARGET
sudo command          run one command elevated; authorizes the CURRENT user via /etc/sudoers

john file / john --show file     crack password hashes by testing candidates
```
