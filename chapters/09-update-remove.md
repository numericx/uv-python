# Chapter 9 — Updating and Removing Dependencies

## Keeping dependencies up to date

Python packages evolve. New versions fix bugs, add features, and sometimes break compatibility. `uv` gives you several ways to stay current.

## Upgrading all dependencies

```bash
uv lock --upgrade
```

This resolves the latest compatible versions of every dependency in your `pyproject.toml` and writes them to `uv.lock`. Then sync:

```bash
uv sync
```

To upgrade everything and see what changed:

```bash
uv lock --upgrade && uv diff   # Show the diff of what changed
```

## Upgrading a single package

```bash
uv lock --upgrade-package requests
uv sync
```

This upgrades only `requests` to the latest version allowed by your constraints, leaving everything else unchanged.

## Removing a dependency

```bash
uv remove pydantic
```

This:
1. Removes `"pydantic>=..."` from `pyproject.toml`
2. Regenerates `uv.lock` without it
3. Removes it from `.venv/`

All in one command. No manual editing.

## Updating a dependency to a specific version

```bash
# Pin to an exact version
uv add "requests==2.32.0"

# Allow a range (same as normal uv add)
uv add "requests>=2.31,<3"

# Drop the constraint entirely (any version works)
uv add "requests@"    # The @ means "latest compatible"
```

## Using `--frozen` in practice

`--frozen` tells uv: **"don't touch pyproject.toml or uv.lock."**

| Scenario | Should you use --frozen? | Why |
|----------|------------------------|-----|
| CI pipeline | Always | Ensures reproducible builds |
| Production deployment | Always | Prevents drift from lock file |
| Adding a new dependency | No | You're modifying pyproject.toml intentionally |
| Running tests on existing code | Yes | Verifies the current lock state is valid |

## Troubleshooting common issues

### "Package not found in the cache" (offline mode)

```bash
uv add some-package --offline
# Error: Package not found in the cache
```

Fix: remove `--offline` or run `uv sync` to fetch packages first.

### Version conflicts

If two packages need incompatible versions of a third, uv fails with a clear conflict message:

```
error: Failed to resolve lock file
  × Resolution failed: version conflict detected for package X
```

Fix: adjust your constraints in `pyproject.toml`, then run `uv sync` again.

### Corrupted `.venv/`

```bash
rm -rf .venv
uv sync
```

This rebuilds the environment from scratch using cached wheels.

---

## Exercise

### Step 1: Remove a dev dependency

```bash
cd hello-uv
uv remove ruff
cat pyproject.toml    # Verify ruff is gone from [dependency-groups]
```

### Step 2: Add it back with a specific version range

```bash
uv add "ruff>=0.5,<1"
cat pyproject.toml    # Verify the constraint is set
```

### Step 3: Upgrade just requests to latest

```bash
uv lock --upgrade-package requests
head -20 uv.lock        # Look at the new requests version entry
uv sync
```

### Step 4: Practice with `--frozen`

```bash
# Intentionally dirty the project
echo "" >> pyproject.toml

# This should fail
uv run --frozen python hello_uv/cli.py

# Fix it
uv lock && uv sync

# Now frozen works again
uv run --frozen python hello_uv/cli.py
```

---

## Verify

- [ ] `uv remove <pkg>` removes it from pyproject.toml, uv.lock, and .venv in one command
- [ ] `uv lock --upgrade` updates all packages to latest compatible versions
- [ ] `uv lock --upgrade-package <pkg>` upgrades just one package
- [ ] `--frozen` prevents any modification to pyproject.toml or uv.lock
- [ ] You can resolve a version conflict by adjusting constraints and re-syncing

If all checks pass, move on to the capstone project in Chapter 10.
