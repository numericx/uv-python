# uv — The Fast Python Package Manager

## A hands-on course for beginners

This course teaches you **uv** (pronounced "you-vee"), an extremely fast Python package and project manager written in Rust by [Astral](https://astral.sh). It replaces `pip`, `pip-tools`, `pipx`, `poetry`, `pyenv`, `virtualenv`, and more — all with a single CLI tool that's typically **10–100× faster**.

No prior experience with package management is required. You'll build real projects from day one.

---

## Who this is for

- Developers who want a simpler, faster way to manage Python projects
- People tired of `pip`'s slowness or poetry's complexity
- Anyone writing Python scripts that need reproducible environments

You **don't** need to know:
- virtualenv, pipenv, conda, or poetry — this course starts from zero
- how to activate a virtual environment (you won't need to)

---

## Course structure

| Module | Chapters | Estimated time | What you'll learn |
|--------|----------|----------------|-------------------|
| **Foundations** | 1–4 | ~2 hours | Installation, project scaffolding, dependencies, running code |
| **Daily Workflows** | 5–9 | ~3 hours | Python versions, CLI tools, script mode, caching, updates |
| **Capstone** | 10 | ~1 hour | Build a complete project from scratch using everything you've learned |

**Total time:** ~6–7 hours. Work at your own pace — there are no deadlines.

## Contents

- [Installation](chapters/01-installation.md)
- [First Project](chapters/02-first-project.md)
- [Dependencies](chapters/03-dependencies.md)
- [Running Code](chapters/04-running-code.md)
- [Python Versions](chapters/05-python-versions.md)
- [Tools and uvx](chapters/06-tools-and-uvx.md)
- [Script Mode](chapters/07-script-mode.md)
- [Caching and Performance](chapters/08-caching-performance.md)
- [Update and Remove](chapters/09-update-remove.md)
- [Capstone Project](chapters/10-capstone.md)

---

## Prerequisites

- A Linux, macOS, or Windows (with WSL2) machine
- `curl` installed (for the installer)
- No existing Python environment required — uv ships its own

---

## How this course works

Each chapter follows the same pattern:

1. **Concept** — a brief explanation of what you're learning and why
2. **Demo** — copy-pasteable commands with expected output
3. **Exercise** — your turn to do it yourself
4. **Verify** — a checklist confirming you got it right

**Important rule:** never skip the exercise. The concepts are simple — the value is in building muscle memory with the commands.

---

## What you'll be able to do by the end

- Create a Python project from scratch in under 30 seconds
- Add, remove, and update dependencies without ever touching a virtual environment manually
- Run code in reproducible environments with one command (`uv run`)
- Install CLI tools for all your projects with `uv tool`
- Write standalone Python scripts that manage their own dependencies
- Understand why `uv` is so fast and how to keep it fast

---

## File layout

```
uv-python/
  LICENSE
  README.md                  # You are here
  chapters/
    01-installation.md       # What is uv + install
    02-first-project.md      # uv init + pyproject.toml anatomy
    03-dependencies.md       # uv add, sync, lock
    04-running-code.md       # uv run in practice
    05-python-versions.md    # uv python pin + install
    06-tools-and-uvx.md      # uv tool install, uvx
    07-script-mode.md        # uv run --script
    08-caching-performance.md # uv cache, why it's fast
    09-update-remove.md      # uv remove, upgrade, --frozen
    10-capstone.md           # Full end-to-end exercise
  exercises/
    README.md                # Capstone project requirements
```

---

## Getting help

- `uv help` — list all commands
- `uv help <command>` — detailed help for a specific command
- [uv documentation](https://docs.astral.sh/uv/) — the canonical reference

If something goes wrong, run `uv self update` to make sure you're on the latest version.
