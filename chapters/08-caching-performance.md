# Chapter 8 — Caching and Performance

## Why is uv so fast?

It comes down to three things:

| Mechanism | What it does | Benefit |
|-----------|-------------|---------|
| **Concurrent downloads** | Downloads wheels from PyPI in parallel | Network speed, not serial I/O, is your limit |
| **FFI (Foreign Function Interface) wheels** | Ships pre-compiled native code as Python-compatible `.abi3` wheels | No compilation on install — `import numpy` just works |
| **Smart caching** | Caches wheels, metadata, and Python binaries globally across projects | Second project with same deps installs in milliseconds |

## Understanding the cache

uv stores its cache at:

```
~/.cache/uv/           # Linux / macOS (XDG_CACHE_HOME/uv)
%LOCALAPPDATA%\uv\cache\   # Windows
```

Inside, you'll find:

```
~/.cache/uv/
├── archives/          # Downloaded wheels
├── build-suffixes/    # Compiled package builds (rarely needed)
└── python-install-dir/  # Managed CPython downloads
```

This cache is **shared across all projects on your machine**. Install `requests` in one project, then install it in another — the second project uses the cached wheel instantly.

## Cache management commands

| Command | What it does |
|---------|-------------|
| `uv cache dir` | Show the cache directory path |
| `uv cache prune` | Remove unused cached wheels (saves disk space) |
| `uv cache prune --ci` | Remove all entries not used by any project (aggressive CI mode) |
| `uv cache clean` | Same as `uv cache prune` with no arguments |

### When to prune

You rarely need to prune. uv's cache grows slowly because it deduplicates identical wheels across projects. Prune when:

- You're low on disk space
- A wheel got corrupted (rare)
- Running in CI with `uv cache prune --ci` between jobs

```bash
du -sh ~/.cache/uv   # Check your cache size before pruning
uv cache prune         # Remove unused entries
du -sh ~/.cache/uv   # Cache should be smaller now
```

## Performance tips for your workflow

### Tip 1: Never use `pip install` in a uv project

```bash
# Wrong — bypasses uv's resolver and cache
pip install requests

# Right — uses uv's resolver, cache, and lock file
uv add requests
```

Using pip defeats the purpose. It writes to the wrong location, ignores your lock file, and can't leverage cached wheels.

### Tip 2: Use `--frozen` in CI or production

```bash
uv sync --frozen    # Install exactly what uv.lock says — nothing more, nothing less
```

This skips network resolution entirely, making builds faster and fully reproducible.

### Tip 3: Keep a single `.venv/` per project

Don't create multiple virtual environments. `uv sync` updates the existing one in place. If you want a fresh environment, delete `.venv/` first:

```bash
rm -rf .venv
uv sync             # Fresh install (slower)
uv sync             # Second run — uses cached wheels (fast)
```

### Tip 4: Use `--no-cache` only for debugging

If something weird happens with a dependency:

```bash
UV_NO_CACHE=1 uv add requests    # Forces a fresh download (slow, but useful for debugging)
```

---

## Exercise

### Step 1: Check your cache

```bash
uv cache dir        # Where is the cache?
du -sh ~/.cache/uv  # How much space does it use?
```

### Step 2: Create a second project that reuses cached wheels

```bash
cd /tmp
uv init hello-uv-two
cd hello-uv-two
uv add requests    # Should be nearly instant (wheel is already cached)
```

Compare the speed of this install with the one in Chapter 3. The difference is the cache at work.

### Step 3: Prune and verify

```bash
du -sh ~/.cache/uv           # Note the size
uv cache prune                # Remove unused entries
du -sh ~/.cache/uv            # Size should be smaller
```

### Step 4: Simulate `--frozen` in CI

```bash
cd hello-uv
# Make pyproject.toml "dirty" (add a comment)
echo "# dev note" >> pyproject.toml
uv sync --frozen   # Should fail with error about uv.lock being out of date
uv lock             # Fix: regenerate lock file
uv sync --frozen   # Now it works
```

---

## Verify

- [ ] `uv cache dir` shows the cache location
- [ ] Cached wheels are shared across projects (second install is faster)
- [ ] `uv cache prune` removes unused entries safely
- [ ] `--frozen` skips network resolution and fails if out of sync
- [ ] You understand why pip should never be used inside a uv project

If all checks pass, move on to Chapter 9.
