# Chapter 5 — Python Version Management

## Why manage Python with uv?

Traditionally, managing Python versions was a separate tool's job (`pyenv`, `conda`, system package managers). Each had friction: pyenv needed compilation on some platforms, conda was heavy, and system packages couldn't coexist.

uv solves this with **managed downloads**. When you ask uv for a Python version, it downloads a self-contained CPython binary from astral.sh — no compiler, no `apt`, no `brew`. These binaries include pip, setuptools, and wheel built in.

## Installing a Python version

```bash
uv python install 3.12
```

Output:

```
Downloaded CPython 3.12.4
```

You can install multiple versions side by side:

```bash
uv python install 3.11
uv python install 3.13
uv python list    # Show all installed versions
```

## Pinning a version for a project

In any project directory, set the default Python version:

```bash
uv python pin 3.12
```

This creates or updates `.python-version` in the current directory:

```
my-first-project/
├── .python-version   # ← now contains "3.12"
└── ...
```

Every `uv` command in this directory (and subdirectories) will use Python 3.12. You can override per-command:

```bash
uv add some-package -p 3.13   # Use 3.13 just for this operation
```

## Where uv stores managed Pythons

```
~/.local/share/uv/python/    # On Linux
~/Library/Caches/uv/python/  # On macOS
%LOCALAPPDATA%\uv\python\    # On Windows
```

These are regular directories — you can `ls` them to see what uv manages. Never edit the contents by hand; use `uv python` commands.

## The `--python` flag: specify version on the fly

Instead of pinning, pass `--python` (or `-p`) directly:

```bash
uv init my-project --python 3.13    # Create a project targeting 3.13
uv add requests -p 3.12             # Add deps using 3.12 for this operation
uv run python script.py --python 3.11   # Run with 3.11 just this once
```

## Removing a Python version

```bash
uv python uninstall 3.11
```

---

## Exercise

### Step 1: Install a Python version (if none are available)

```bash
uv python install 3.12
uv python list   # Confirm it appears in the output
```

If you already have a managed Python, install a different one (e.g., `uv python install 3.11`).

### Step 2: Pin to a version in your project

```bash
cd hello-uv
uv python pin 3.12
cat .python-version   # Should print "3.12"
```

### Step 3: Verify `uv run` respects the pin

```bash
uv run python --version    # Should say Python 3.12.x
```

### Step 4: Override with `-p`

```bash
uv run -p 3.12 --no-sync python --version   # Explicit override, no sync
```

---

## Verify

- [ ] `uv python install <version>` downloads a managed CPython binary
- [ ] `uv python list` shows installed versions
- [ ] `uv python pin <version>` sets the project's `.python-version`
- [ ] `uv run` uses the pinned version by default
- [ ] `-p <version>` overrides the pin for a single command
- [ ] You can describe where managed Pythons are stored

If all checks pass, move on to Chapter 6.
