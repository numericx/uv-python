# Chapter 10 — Capstone Project

## Build a complete project from scratch using everything you've learned

This is your final exam. You'll go from an empty directory to a fully working Python project in about 30 minutes. Follow the steps below and verify each checkpoint before moving on.

**Time estimate:** 30–45 minutes

---

## The goal

Build a small CLI tool called `wordcount` that:
- Takes a file path as input
- Counts lines, words, and characters
- Optionally formats output as JSON

You'll learn the full workflow by doing it.

## Step 1 — Create the project

```bash
cd /tmp
uv init wordcount -d "A simple word count CLI tool"
cd wordcount
```

### Checkpoint 1

- [ ] `pyproject.toml` exists with `name = "wordcount"`
- [ ] `.python-version` contains a version number
- [ ] `main.py` and `README.md` were created

## Step 2 — Pin Python and add dependencies

```bash
uv python pin 3.12
uv add click        # CLI argument parsing
uv add --dev pytest ruff
```

### Checkpoint 2

- [ ] `pyproject.toml` has a `dependencies` list containing `click`
- [ ] `[dependency-groups] dev` contains `pytest` and `ruff`
- [ ] `.venv/` directory exists

## Step 3 — Write the application code

Create `wordcount/main.py`:

```python
"""Word count CLI tool."""

import json
import sys
from pathlib import Path


def count(text: str) -> dict:
    """Count lines, words, and characters in the given text."""
    return {
        "lines": text.count("\n") + (1 if text and not text.endswith("\n") else 0),
        "words": len(text.split()),
        "characters": len(text),
    }


def format_output(result: dict, as_json: bool) -> str:
    """Format the result as plain text or JSON."""
    if as_json:
        return json.dumps(result, indent=2)
    lines = f"  Lines:       {result['lines']}"
    words = f"  Words:       {result['words']}"
    chars = f"  Characters:  {result['characters']}"
    return f"{lines}\n{words}\n{chars}\n"


def main() -> int:
    """Entry point for the CLI."""
    import click

    @click.command()
    @click.argument("file", type=click.Path(exists=True))
    @click.option("--json", is_flag=True, help="Output as JSON")
    def cli(file: str, json: bool) -> None:
        """Count lines, words, and characters in a file."""
        text = Path(file).read_text(encoding="utf-8")
        result = count(text)
        print(f"File: {file}")
        print(format_output(result, as_json=json))

    cli()
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

### Checkpoint 3

- [ ] `wordcount/main.py` exists with the code above
- [ ] The script imports `click`, `json`, and uses `pathlib` (stdlib)

## Step 4 — Write tests

Create `tests/test_main.py`:

```python
"""Tests for wordcount.main."""

from wordcount.main import count, format_output


def test_count_basic():
    result = count("hello world\nfoo bar")
    assert result["lines"] == 2
    assert result["words"] == 4
    assert result["characters"] == 15


def test_count_empty():
    result = count("")
    assert result["lines"] == 0
    assert result["words"] == 0
    assert result["characters"] == 0


def test_format_output_plain():
    result = {"lines": 1, "words": 2, "characters": 3}
    output = format_output(result, as_json=False)
    assert "Lines:" in output
    assert "Words:" in output
    assert "Characters:" in output


def test_format_output_json():
    result = {"lines": 1, "words": 2, "characters": 3}
    output = format_output(result, as_json=True)
    import json
    parsed = json.loads(output)
    assert parsed == result
```

### Checkpoint 4

- [ ] `tests/test_main.py` exists with at least 4 tests
- [ ] All tests import from `wordcount.main` (not relative imports)

## Step 5 — Run the project

```bash
# Create a test file
echo -e "hello world\nfoo bar" > test_input.txt

# Run the CLI tool
uv run python main.py test_input.txt

# Run as JSON
uv run python main.py test_input.txt --json

# Run tests
uv run pytest

# Run linter
uv run ruff check .
uv run ruff format --check .
```

### Checkpoint 5

- [ ] `uv run python main.py test_input.txt` prints word count results
- [ ] `uv run pytest` passes all 4 tests
- [ ] `uv run ruff check .` reports no errors
- [ ] No virtual environment was manually activated at any point

## Step 6 — Verify the full workflow

```bash
# Check lock file is current
uv sync --frozen          # Should succeed (nothing dirty)

# Add a new dependency to prove --frozen fails
uv add tomli              # Modifies pyproject.toml and uv.lock
uv run --frozen python main.py test_input.txt   # Should FAIL

# Fix it
uv sync                   # Re-sync the environment
uv run --frozen python main.py test_input.txt   # Should succeed again
```

### Final checkpoint

- [ ] `uv sync --frozen` succeeds (environment matches lock file)
- [ ] `uv add` modifies pyproject.toml and uv.lock automatically
- [ ] The project is fully buildable: init → add deps → write code → run → test → lint

---

## You're done!

If all checkpoints passed, you now know how to:

| Skill | Chapter |
|-------|---------|
| Install uv | 1 |
| Create a project with `uv init` | 2 |
| Add/remove/update dependencies | 3, 9 |
| Manage the lock file | 3 |
| Run code with `uv run` | 4 |
| Pin and install Python versions | 5 |
| Install CLI tools with `uv tool` | 6 |
| Write self-contained scripts with `uvx` / `--script` | 7 |
| Manage uv's cache | 8 |
| Use `--frozen` for reproducible builds | 9 |

**Next steps:**
- Try the script mode from Chapter 7 on this project (write a one-off utility)
- Install more tools: `uv tool install black`, `uv tool install mypy`
- Read the [uv documentation](https://docs.astral.sh/uv/) for advanced topics (workspaces, monorepos, CI/CD)
