# Linux Package Management

## Packages, Repositories, and Package Managers

A **package** is a downloadable bundle containing the software needed to install an application. A **repository** is a source of installable packages for a given distribution. A **package manager** installs, removes, updates, and reports on packages — and if something isn't available in the configured repositories, it may need to be downloaded and installed manually instead (see the `dpkg` sections below).

| Distribution Family | Package Manager | Package Extension |
|---|---|---|
| Debian, Ubuntu, Linux Mint | APT / `dpkg` | `.deb` |
| RHEL, Fedora, CentOS | RPM | `.rpm` |

This chapter focuses on the Debian/Ubuntu side — APT and `dpkg` — since that's the family most commonly encountered.

## Where APT Looks: `/etc/apt/sources.list`

```bash
cat /etc/apt/sources.list
```

This file lists the repository addresses APT downloads packages from.

## Updating vs. Upgrading — a Distinction Worth Keeping Straight

```bash
sudo apt-get update    # refresh LOCAL knowledge of what's available in the repos
sudo apt-get upgrade   # actually install newer versions of packages already on the system
```

These sound similar but do genuinely different things: `apt-get update` only refreshes APT's local package *index* — it doesn't touch any installed software. `apt-get upgrade` is the step that actually applies newer versions, and it shows what will change (packages affected, disk space involved) and asks for confirmation before proceeding. In practice the two are almost always run back-to-back (`update` first, so `upgrade` knows what's actually new).

## Installing and Removing with APT

```bash
sudo apt install chromium    # install from a configured repository
sudo apt remove chromium     # remove an installed package
apt show firefox             # view details: version, dependencies, description, size, etc.
```

APT resolves dependencies automatically, shows the planned changes, and asks for confirmation before installing or removing anything.

## Working Directly with `dpkg`

`dpkg` operates one level lower than APT — it manages `.deb` package files directly, without any awareness of repositories.

```bash
dpkg -l                     # list installed packages (name + status)
sudo dpkg -i hwinfo.deb     # install a local .deb file directly (-i = install)
sudo dpkg -r hwinfo         # remove an installed package (-r = remove)
```

One detail worth remembering: when removing with `dpkg -r`, use the **package name** exactly as `dpkg -l` shows it — not the original `.deb` filename, which may not match.

## APT vs. `dpkg`

| Feature | APT | `dpkg` |
|---|---|---|
| Repository management | Yes | No |
| Install/remove packages | Yes | Yes |
| Update repository info | Yes | No |
| Dependency management | Yes | Limited |
| Install local `.deb` files | Yes (with the right subcommand) | Yes |
| Package format | `.deb` | `.deb` |

The short version: APT is the higher-level tool that also handles repositories and dependencies; `dpkg` is the lower-level tool that directly manipulates `.deb` files, which is exactly why `dpkg -i` is the fallback when a package isn't in any configured repository at all.

## Related: File 05 (Archive File Formats) for installing a missing archive tool (e.g. `unrar`) the same way; file 17 (Service Management) for what typically happens right after installing a package that runs as a background service.

## Cheat Sheet

```
cat /etc/apt/sources.list        view configured repositories

sudo apt-get update              refresh local package index (no install/upgrade)
sudo apt-get upgrade             actually upgrade installed packages

sudo apt install <pkg>           install from a repository
sudo apt remove <pkg>            remove a package
apt show <pkg>                   view package details

dpkg -l                          list installed packages
sudo dpkg -i <pkg>.deb           install a local .deb file
sudo dpkg -r <pkg>               remove (use the NAME from dpkg -l, not the .deb filename)

APT  = repositories + dependencies + .deb            dpkg = direct, low-level .deb management
```
