# Environment Setup & Configuration

## Installing Python

Python runs on **Linux, macOS, and Windows**, and code is generally portable between platforms except for platform-specific cases.

```bash
python --version
python3 --version
```

**Linux** — often preinstalled; otherwise:
```bash
sudo dnf install python3          # Red Hat–based: Fedora, CentOS (older: yum)
sudo apt-get update && sudo apt-get install python3    # Debian-based: Ubuntu, Kali
```

**macOS:**
```bash
brew install python                # Homebrew
```
Also available via Xcode/developer tools, or the official installer (PKG) from python.org.

**Windows:** run the official installer, and during setup enable **"Add python.exe to PATH"** — this is what lets you run `python` directly from a terminal — then choose Install Now. Verify with `python --version` in Command Prompt.

## IDEs and Text Editors

An **IDE** bundles tools for writing, debugging, testing, and managing code: autocomplete, error detection, step-through debugging, testing support, project organization, and integration with version control.

| Tool | Notes |
|---|---|
| **PyCharm** | JetBrains; free Community Edition + paid Professional (adds Django/Flask support); cross-platform |
| **VS Code** | Microsoft, free, open-source; extension-based; supports many languages, not just Python |
| **Jupyter Notebook** | Interactive; code in independently-runnable **cells**, output shown inline; strong for data analysis/visualization; exportable to HTML/PDF |
| **Atom** | GitHub's free editor; Python support via plugins (Linter, Hydrogen, Script, Atom REPL) |

## Virtual Environments

A **virtual environment** isolates a project's packages/dependencies from other projects, so different projects can use different library versions without conflict.

| | Pro | Con |
|---|---|---|
| Virtual environments | Dependency isolation, per-project versions, independent setups | Extra disk space, some added management overhead |

```bash
pip install virtualenv
virtualenv my_env
source my_env/bin/activate     # shell prompt shows (my_env) once active
deactivate
```
Modern Python also ships the built-in `venv` module, so `virtualenv` isn't strictly required for basic use.

## Package Management: `pip` vs. `Conda`

**`pip`** — Python's standard installer, pulling from **PyPI (Python Package Index)**:
```bash
pip install package_name
```

**Conda** — a package *and* environment manager that also handles non-Python dependencies and other languages (R, Julia), with built-in environment management.

| | pip | Conda |
|---|---|---|
| Python packages | ✓ | ✓ |
| Non-Python packages | Limited | ✓ |
| Environment management | Via `venv`/`virtualenv` | Built-in |
| Multi-language environments | Limited | ✓ |

For typical Python projects, **`pip` + `venv`** is usually sufficient; Conda earns its extra weight mainly for data-science stacks with non-Python dependencies.

## Cheat Sheet

```
python --version / python3 --version    check installed version
brew install python / apt-get install python3 / dnf install python3

Virtual env:  virtualenv name  →  source name/bin/activate  →  deactivate
              (or built-in venv module)

pip install package        install from PyPI
Conda                       package + env manager, handles non-Python deps too

IDEs: PyCharm (full-featured) / VS Code (extensible) / Jupyter (cell-based, data-focused) / Atom (plugin-based)
```
