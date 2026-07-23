# Chapter 2 — Your First Project

## Creating a project from nothing

Before `uv`, creating a new Python project meant:

1. Create a directory by hand
2. Write a `pyproject.toml` manually (or copy-paste one)
3. Run `python -m venv .venv`
4. Activate it (`source .venv/bin/activate`)
5. Install dependencies one by one with `pip install`
6. Remember to activate it every time you come back

With uv, it's **one command**:

```bash
cd /tmp
uv init my-first-project
cd my-first-project
```

That's it. Here's what happened:

## What `uv init` created

```
my-first-project/
├── .gitignore       # Git ignores for Python projects
├── .python-version  # Tells uv which Python to use (e.g., "3.12")
├── README.md        # Basic project description
├── main.py          # A "Hello, world!" entry point
└── pyproject.toml   # Project metadata and dependency declarations
```

Let's look at the key file — `pyproject.toml`:

```toml
[project]
name = "my-first-project"
version = "0.1.0"
description = "Add your description here"
readme = "README.md"
requires-python = ">=3.12"
dependencies = []

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

### Key sections explained

| Section | What it does |
|---------|-------------|
| `[project].name` | The package name (used when installing your project) |
| `[project].version` | Current version (bump this with each release) |
| `[project].requires-python` | Minimum Python version required |
| `dependencies` | List of packages your project needs to run |
| `[build-system]` | Tells uv which tool builds your package (hatchling is the default) |

## The virtual environment

When you add a dependency for the first time, uv automatically creates a virtual environment in `.venv/`:

```
my-first-project/
├── .venv/             # ← created by uv, never edit manually
│   ├── bin/
│   ├── lib/
│   └── ...
├── pyproject.toml
├── main.py
└── ...
```

**Important:** You never activate `.venv` manually. uv handles it for you through `uv run`. We'll cover that in Chapter 4.

## Try it yourself

### Step 1: Create the project

```bash
uv init hello-uv
cd hello-uv
ls -la
```

You should see: `.gitignore`, `.python-version`, `README.md`, `main.py`, `pyproject.toml`

### Step 2: Look at what was created

```bash
cat pyproject.toml
cat .python-version
cat main.py
```

The `.python-version` file tells uv to use Python 3.12 for this project. The `main.py` file already contains a working script — but it won't run yet because no Python interpreter is installed (we'll fix that in Chapter 5).

### Step 3: See the project structure

```bash
find . -not -path './.git/*' | sort
```

Expected output:

```
.
./README.md
./main.py
./pyproject.toml
./.gitignore
./.python-version
```

## Understanding `uv init` flags

You can customize what `uv init` creates:

| Flag | What it does |
|------|-------------|
| `--no-readme` | Skip creating README.md |
| `--no-workspace` | Don't add the project to a workspace (skip for now) |
| `-d, --description` | Set the project description directly |

Example:

```bash
uv init my-project -d "A CLI tool that does something useful"
```

---

## Exercise

1. Create a new project called `hello-uv` in `/tmp` (or any temp directory)
2. Look at each file uv created (`cat` or your editor of choice)
3. Modify `pyproject.toml`: change the description to `"My uv course project"`
4. Check that `.python-version` contains a Python version number

### Verify

- [ ] You can create a new uv project with one command (`uv init`)
- [ ] You understand what each file in a uv project does
- [ ] `pyproject.toml` has `name`, `version`, `requires-python`, and `dependencies`
- [ ] `.python-version` stores the pinned Python version
- [ ] No `.venv/` directory exists yet (it appears only when you add dependencies)

If all checks pass, move on to Chapter 3.
