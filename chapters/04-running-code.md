# Chapter 4 — Running Code in Your Project

## The problem with manual virtual environment activation

Before uv, the workflow was:

```bash
source .venv/bin/activate   # Activate manually
python main.py              # Run code
deactivate                   # Remember to deactivate later
```

If you forget `deactivate`, your global environment gets polluted. If you activate in the wrong directory, you run the wrong packages. These are boring, repetitive tasks that should never exist.

## The uv way: `uv run`

```bash
uv run python main.py
```

That's it. uv automatically:

1. Finds or creates `.venv/` in the project directory
2. Ensures all dependencies from `pyproject.toml` are installed (via `uv sync`)
3. Runs your command inside that environment

No activation, no deactivation, no forgetting to activate.

## Running any installed command

Since uv installs packages into `.venv`, you can run them directly:

```bash
uv run ruff check .          # Lint the project
uv run pytest tests/          # Run tests
uv run python -m http.server  # Start a server (any CLI tool works)
```

All three commands execute inside the managed environment with all dependencies available.

## `uv run` flags

| Flag | What it does |
|------|-------------|
| `--frozen` | Don't update `uv.lock`; fail if it's out of sync (use in CI) |
| `--no-sync` | Run without syncing (assumes `.venv` already exists) |
| `-p <version>` | Use a specific Python version instead of the project's pinned version |

### When to use `--frozen`

In CI or production, you **always** want `--frozen`. It ensures your lock file is authoritative — nothing sneaks in:

```bash
uv run --frozen pytest tests/
```

If `pyproject.toml` has been modified since the last `uv sync`, this command fails with a clear error instead of silently installing new packages.

## The `-p` flag: pick any Python version

Want to test your code against Python 3.13 without changing your project's default?

```bash
uv run -p 3.13 --no-sync python --version
```

This runs `python --version` with Python 3.13 but **does not change** the project's `.python-version`. Useful for testing compatibility before you commit to an upgrade.

## Try it yourself

### Step 1: Add a dependency you can exercise

```bash
cd hello-uv
uv add requests
```

### Step 2: Write a small script that uses it

Create `hello_uv/cli.py`:

```python
import requests

def greet(name: str) -> str:
    """Return a greeting message."""
    return f"Hello, {name}!"

if __name__ == "__main__":
    print(greet("World"))

    # Prove that requests is actually installed in this environment
    resp = requests.get("https://httpbin.org/get")
    print(f"httpbin responded with status {resp.status_code}")
```

### Step 3: Run it with uv

```bash
uv run python hello_uv/cli.py
```

Expected output:

```
Hello, World!
httpbin responded with status 200
```

Notice you never activated a virtual environment.

### Step 4: Try with `--frozen`

```bash
uv run --frozen python hello_uv/cli.py   # Should work (already synced)
uv add httpx                              # Now pyproject.toml is "dirty"
uv run --frozen python hello_uv/cli.py   # Fails — lock file out of date!
uv sync                                   # Fix: sync the environment
```

---

## Exercise

1. Create a script `hello_uv/tools.py` that imports both `requests` and `pydantic`
2. Run it with `uv run python hello_uv/tools.py` (no venv activation)
3. Verify that `ruff` works as a command: `uv run ruff check .`
4. Run `uv run --frozen pytest --co` to list available tests without executing

### Verify

- [ ] `uv run <command>` executes inside the managed environment without activation
- [ ] All dependencies from `pyproject.toml` (including dev) are available
- [ ] `uv run --frozen` fails when `pyproject.toml` and `uv.lock` are out of sync
- [ ] You can run any installed tool (`ruff`, `pytest`) directly through `uv run`

If all checks pass, move on to Chapter 5.
