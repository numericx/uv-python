# Chapter 3 — Dependencies and Lock Files

## Why dependencies matter

Every Python project depends on other packages. Without a reproducible system for managing them, your code breaks when a dependency changes. That's what `pyproject.toml` (the manifest) and `uv.lock` (the lock file) solve together.

- **`pyproject.toml`** — declares *which* versions your project needs (the "intent")
- **`uv.lock`** — records the *exact* versions resolved and installed (the "reality")

## Adding runtime dependencies

```bash
uv add requests
```

Output:

```
Resolved 3 packages in 42ms
Prepared 2 packages in 156ms
Installed 3 packages in 2ms
 + idna==3.7
 + requests==2.32.3
 + urllib3==2.2.3
```

Three things happened at once:

1. uv resolved the dependency tree (requests → idna, urllib3)
2. It created `.venv/` and installed everything into it
3. It wrote `pyproject.toml` and `uv.lock`

Look at `pyproject.toml` now:

```toml
[project]
name = "hello-uv"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "requests>=2.32.3,<3",
]
```

Look at `uv.lock` — it's a complete snapshot of every package version installed:

```toml
version = 1
requires-python = ">=3.12"
option = []

[[package]]
name = "requests"
version = "2.32.3"
source = { registry = "https://pypi.org/simple" }
dependencies = [
    { name = "idna" },
    { name = "urllib3" },
]
```

The lock file is human-readable but you **never edit it by hand**. Use `uv sync` to regenerate it.

## Adding dev dependencies

Dev dependencies are packages needed only for development (testing, linting, etc.), not for running the project:

```bash
uv add --dev pytest ruff
```

This adds them under `[dependency-groups]`:

```toml
[dependency-groups]
dev = [
    "pytest>=8.3.3,<9",
    "ruff>=0.6.4,<1",
]
```

## Key commands at a glance

| Command | What it does |
|---------|-------------|
| `uv add <pkg>` | Add to runtime dependencies + sync venv |
| `uv add --dev <pkg>` | Add to dev dependencies + sync venv |
| `uv sync` | Install/update all deps in `.venv` to match `uv.lock` |
| `uv lock` | Regenerate `uv.lock` from `pyproject.toml` (doesn't install) |
| `uv remove <pkg>` | Remove a dependency and sync again |

## `sync` vs `lock`: the difference

```bash
uv lock   # Updates uv.lock only — fast, no installation
uv sync   # Installs everything in .venv to match uv.lock — slower
```

Use `uv lock` when you just want to update the file (e.g., on CI). Use `uv sync` when you need a working environment.

## Updating dependencies

To upgrade all packages:

```bash
uv lock --upgrade
```

To upgrade just one package:

```bash
uv lock --upgrade-package requests
```

---

## Exercise

Start in your `hello-uv` project from Chapter 2.

### Step 1: Add runtime and dev dependencies

```bash
uv add requests pydantic
uv add --dev pytest ruff
```

### Step 2: Inspect the result

```bash
cat pyproject.toml
ls uv.lock
head -40 uv.lock
```

Verify that `pyproject.toml` has both a `dependencies` list and a `[dependency-groups] dev` section. Verify that `uv.lock` exists.

### Step 3: Try `uv lock` alone

```bash
uv remove pydantic   # Remove one dep
uv lock              # Regenerate the lock file (should be fast)
uv sync              # Re-install to match the updated lock file
```

Check that `pyproject.toml` no longer mentions `pydantic`.

### Step 4: Upgrade a package

```bash
uv lock --upgrade-package requests
head -30 uv.lock   # Check that requests version changed
```

---

## Verify

- [ ] `uv add <pkg>` adds a runtime dependency and creates `.venv/`
- [ ] `uv add --dev <pkg>` adds a dev dependency under `[dependency-groups]`
- [ ] `uv sync` installs all dependencies from the lock file
- [ ] `uv lock` only regenerates the lock file (no installation)
- [ ] `uv remove <pkg>` removes it from both `pyproject.toml` and `.venv`
- [ ] You can describe what `uv.lock` does vs `pyproject.toml`

If all checks pass, move on to Chapter 4.
