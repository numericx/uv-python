# Chapter 1 — What is `uv` and Why Should You Care?

## The problem with today's Python tooling

Python developers juggle a lot of tools. A typical project might need:

| Tool | Purpose | Limitation |
|------|---------|------------|
| `pip` | Install packages | Slow, no lock file, no project management |
| `venv` | Create virtual environments | Manual activation, easy to forget |
| `poetry` / `pipenv` | Manage project dependencies | Complex config, slow resolver |
| `pyenv` | Manage Python versions | Doesn't integrate with deps |
| `pipx` | Install CLI tools globally | No dependency resolution |

Each tool has its own config file, its own workflow, and its own gotchas. They also stack up to **minutes** of setup time for a simple project.

## What `uv` is

**`uv` is a single tool that replaces all of the above.** One CLI, one lock file, one virtual environment — and it's built in Rust.

- **10–100× faster than pip** because it downloads packages concurrently and uses an optimized cache
- **Zero configuration** for projects — `uv init` creates everything you need
- **No manual venv activation** — `uv run` handles it automatically
- **Managed Python installs** — `uv python install 3.12` downloads a standalone CPython binary (no system package manager needed)

## Installation

### On Linux and macOS

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

This installs uv to `~/.local/bin/uv`. If the directory isn't on your PATH, add it:

```bash
# Add to your shell config file (e.g., ~/.bashrc or ~/.zshrc)
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### On Windows (PowerShell)

```powershell
powershell -c "irm https://astral.sh/uv/pwsh/install.ps1 | iex"
```

### Via a package manager (alternative)

| Platform | Command |
|----------|---------|
| Homebrew (macOS / Linux) | `brew install uv` |
| AUR (Arch Linux) | `yay -S uv` |
| npm | `npm install -g uv` |
| Docker | `docker pull ghcr.io/astral-sh/uv:latest` |

## Verify the installation

```bash
uv --version
```

Expected output (your version number may differ):

```
uv 0.7.0
```

Also check that uv can find Python:

```bash
uv python list
```

This should list your available Python interpreters (or tell you none are found — which is fine, we'll install one in Chapter 5).

---

## Exercise

1. Install uv using the method above for your platform
2. Run `uv --version` and confirm it prints a version number
3. Try `uv help` to see the list of available commands
4. Pick any command from that list and run `uv help <command>` (e.g., `uv help init`)

### Verify

- [] `uv --version` prints a version ≥ 0.5.0
- [] `uv help` lists at least 15 commands
- [] You can describe uv in one sentence: what is it and what does it replace?

> **One-sentence answer:** "uv is a fast Python package manager that replaces pip, poetry, pyenv, and pipx with a single tool."

If you got these checks, move on to [Chapter 2](/chapters/02-first-project.md).
