# Chapter 6 — Working with Tools

## Two worlds: project deps vs. CLI tools

A Python package has two kinds of consumers:

| | Project dependencies | CLI tools |
|---|---------------------|-----------|
| **What** | Libraries your code imports (requests, pydantic) | Standalone commands (ruff, black, bandit) |
| **Where installed** | `.venv/` in the project | A user-wide tool directory |
| **How accessed** | Automatically via `uv run` | As a global command (`ruff`, not `uv run ruff`) |

uv handles both worlds. Project dependencies go into `pyproject.toml` (covered in Chapter 3). CLI tools live outside your project entirely.

## Installing a tool

```bash
uv tool install ruff
```

Output:

```
Resolved 1 package in 5ms
Prepared 1 package in 200ms
Installed 1 package in 2ms
 + ruff==0.6.4
Installed executable ruff (does not update shell)
```

The tool is installed to `~/.local/bin/` (Linux) or equivalent, and is immediately available on the command line:

```bash
ruff --version    # Works globally, outside any project
```

## Updating and removing tools

```bash
uv tool upgrade ruff        # Upgrade a single tool to latest allowed version
uv tool upgrade --all        # Upgrade all installed tools
uv tool uninstall ruff       # Remove the tool completely
```

## Listing installed tools

```bash
uv tool list
```

Output:

```
ruff 0.6.4
black 24.8.0
...
```

## `uvx`: run tools without installing them

Sometimes you need a tool once (or want to test it) without committing to an installation. `uvx` is `uv tool run` — it creates a temporary environment, installs the tool, runs it, and deletes everything:

```bash
uvx ruff check .              # Run ruff in a temporary env (no install needed)
uvx black main.py              # Format a file with black, no install
uvx mypy src/ -p 3.12          # Use a specific Python version for the tool
```

`uvx` is ideal for:
- Trying a tool before deciding to install it permanently
- CI scripts where you don't want leftover tools
- One-off tasks (e.g., "I need `bandit` just for this PR")

## Project deps vs. tools: which should I choose?

Use this decision table:

| You... | Action |
|--------|--------|
| Import a package in your code (`import requests`) | `uv add requests` (project dep) |
| Use a package as a CLI command (`ruff check .`) | `uv tool install ruff` (global tool) OR `uv add --dev ruff` + `uv run ruff` |

**Recommendation:** If the tool is critical to your team's workflow, install it globally with `uv tool install`. If you prefer reproducible environments per project, add it as a dev dependency instead. uv supports both patterns equally well.

---

## Exercise

### Step 1: Install ruff as a tool

```bash
uv tool install ruff
ruff --version    # Should work outside any project directory
```

### Step 2: Install a second tool via `uvx` (no install)

```bash
uvx black --version   # Runs black in a temporary environment
```

### Step 3: List and upgrade tools

```bash
uv tool list            # See what's installed
uv tool upgrade ruff    # Upgrade ruff
ruff --version          # Confirm the version changed
```

### Step 4: Add ruff as a dev dependency in your project (alternative pattern)

```bash
cd hello-uv
uv add --dev ruff
uv run ruff check .     # Run through uv instead of directly
```

Now you've seen both patterns. Use whichever fits your workflow.

---

## Verify

- [ ] `uv tool install <tool>` installs a CLI tool globally
- [ ] The tool runs as a command outside any project (`ruff --version`)
- [ ] `uvx <tool>` runs it in a temporary environment (no permanent install)
- [ ] `uv tool upgrade` updates an installed tool
- [ ] You can choose between global tools (`uv tool install`) and dev dependencies (`uv add --dev`)

If all checks pass, move on to Chapter 7.
