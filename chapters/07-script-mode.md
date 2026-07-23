# Chapter 7 — Script Mode

## The problem with standalone Python scripts

A typical Python script needs dependencies:

```python
# fetch_weather.py
import requests

def get_weather(city: str) -> dict:
    resp = requests.get(f"https://wttr.in/{city}?format=j1")
    return resp.json()
```

How do you ship this? The old way: hope the user has `requests` installed, or include a separate `requirements.txt` to install manually. Neither is ideal.

## uv's answer: inline dependencies in the script itself

uv can manage dependencies per-script. Your Python file declares what it needs right at the top, and uv reads those declarations automatically.

### Method 1: Inline dependency comments (simplest)

```python
#!/usr/bin/env python3
# /// script
# requires-python = ">=3.12"
# dependencies = [
#     "requests",
# ]
# ///

import requests

def get_weather(city: str) -> dict:
    resp = requests.get(f"https://wttr.in/{city}?format=j1")
    return resp.json()

if __name__ == "__main__":
    import json, sys
    city = sys.argv[1] if len(sys.argv) > 1 else "London"
    weather = get_weather(city)
    print(json.dumps(weather["current_condition"][0], indent=2))
```

Run it:

```bash
uv run --script fetch_weather.py
# Or: uvx fetch_weather.py London
```

### Method 2: The shebang shortcut (most convenient)

```python
#!/usr/bin/env -S uv run --script

# /// script
# requires-python = ">=3.12"
# dependencies = [
#     "requests",
# ]
# ///

import requests

def get_weather(city: str) -> dict:
    resp = requests.get(f"https://wttr.in/{city}?format=j1")
    return resp.json()

if __name__ == "__main__":
    import json, sys
    city = sys.argv[1] if len(sys.argv) > 1 else "London"
    weather = get_weather(city)
    print(json.dumps(weather["current_condition"][0], indent=2))
```

Make it executable:

```bash
chmod +x fetch_weather.py
./fetch_weather.py London
```

The shebang `#!/usr/bin/env -S uv run --script` tells the OS to invoke uv with the `--script` flag, which reads inline dependencies and creates a temporary environment automatically. You get **one file that is entirely self-contained** — Python version + all dependencies + code.

## Key fields for `[/// script]` blocks

| Field | Example | Description |
|-------|---------|-------------|
| `requires-python` | `">=3.12"` | Minimum Python version |
| `dependencies` | `["requests"]` | Runtime dependencies (same syntax as pyproject.toml) |
| `dev-dependencies` | `["pytest"]` | Dev dependencies (available via `uv run --script script.py pytest`) |
| `post-run` | `"python cleanup.py"` | Run this after the script finishes |

## When to use script mode

- **CLI utilities** that you share with teammates (one file, works everywhere)
- **Notebooks and prototypes** where setup friction kills momentum
- **CI jobs** that need a quick tool without project scaffolding
- **Reproducible examples** for documentation or troubleshooting

Don't use it for: large projects with many files, packages you publish on PyPI, or anything requiring persistent virtual environments.

---

## Exercise

### Step 1: Write a self-contained script

Create `hello_uv/fetch_weather.py`:

```python
#!/usr/bin/env -S uv run --script

# /// script
# requires-python = ">=3.12"
# dependencies = ["requests"]
# ///

import requests
import json

def get_weather(city: str) -> dict:
    """Fetch weather data for the given city."""
    resp = requests.get(f"https://wttr.in/{city}?format=j1")
    resp.raise_for_status()
    return resp.json()

if __name__ == "__main__":
    import sys
    city = sys.argv[1] if len(sys.argv) > 1 else "London"
    weather = get_weather(city)
    print(f"Weather in {city}:")
    print(f"  Temperature:   {weather['current_condition'][0]['temp_C']}°C")
    print(f"  Condition:     {weather['current_condition'][0]['weatherDesc'][0]['value']}")
    print(f"  Wind:          {weather['current_condition'][0]['windspeedKPH']} km/h")
```

### Step 2: Make it executable and run it

```bash
chmod +x hello_uv/fetch_weather.py
./hello_uv/fetch_weather.py Tokyo
```

Expected: a JSON weather response for Tokyo.

### Step 3: Add a dev dependency to the script

Add `"httpx"` to the dependencies block in the script, then run:

```bash
uv run --script hello_uv/fetch_weather.py   # Should pick up httpx automatically
```

---

## Verify

- [ ] A script with inline `[/// script]` blocks runs without any project setup
- [ ] `#!/usr/bin/env -S uv run --script` shebang makes a script directly executable
- -Dependencies declared in the script are installed automatically
- [ ] You can add dev dependencies to scripts the same way as runtime deps

If all checks pass, move on to Chapter 8.
