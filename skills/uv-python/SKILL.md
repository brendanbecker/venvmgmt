---
name: uv-python
description: Use UV package manager for all Python operations. When running Python scripts, use 'uv run python' instead of 'python'. When installing packages, use 'uv add'. When running tests, use 'uv run pytest'. When starting Jupyter, use 'uv run jupyter'. Apply to any Python project in any location. Handles dependency management, script execution, and tool running with UV.
---

# UV Python Workflow

## Core Principle

**Always use UV commands instead of traditional Python commands.**

UV is a fast, modern Python package manager written in Rust. It's 10-100x faster than pip and provides better dependency resolution.

## Command Mapping

When the user needs to run Python commands, translate them to UV:

| Traditional Command | UV Command |
|-------------------|------------|
| `python script.py` | `uv run python script.py` |
| `python -m module` | `uv run python -m module` |
| `pytest` | `uv run pytest` |
| `pytest tests/` | `uv run pytest tests/` |
| `jupyter lab` | `uv run jupyter lab` |
| `ipython` | `uv run ipython` |
| `black .` | `uv run black .` |
| `ruff check` | `uv run ruff check` |
| `mypy src/` | `uv run mypy src/` |
| `pip install requests` | `uv add requests` |
| `pip install -e .` | `uv sync` |
| `pip uninstall requests` | `uv remove requests` |

## Essential UV Commands

### Project Initialization

```bash
# Initialize UV in existing project (creates pyproject.toml)
uv init

# Initialize with specific Python version
uv init --python 3.11
```

### Dependency Management

```bash
# Add runtime dependency
uv add requests

# Add multiple dependencies
uv add requests pandas numpy

# Add development dependency
uv add --dev pytest black ruff

# Add with version constraint
uv add "requests>=2.31.0"

# Remove dependency
uv remove requests

# Sync dependencies from pyproject.toml (creates/updates .venv)
uv sync

# Sync only production dependencies (no dev)
uv sync --no-dev
```

### Running Code

```bash
# Run Python script
uv run python script.py

# Run Python module
uv run python -m mypackage

# Run with arguments
uv run python script.py --arg1 value --arg2 value

# Run tests
uv run pytest
uv run pytest tests/test_specific.py
uv run pytest -v --cov

# Run Jupyter
uv run jupyter lab
uv run jupyter notebook

# Run interactive Python
uv run ipython
uv run python
```

### One-off Commands (without adding to dependencies)

```bash
# Run tool without installing permanently
uvx ruff check .
uvx black .
uvx mypy src/

# Run script with inline dependencies
uv run --with requests python script.py
```

## Project Workflow

### Starting with Existing Project

When user has an existing Python project:

```bash
# 1. Navigate to project
cd /path/to/existing/project

# 2. Initialize UV (if no pyproject.toml)
uv init

# 3. Add existing dependencies (if they have requirements.txt)
cat requirements.txt | xargs -I {} uv add {}

# 4. Or manually add dependencies
uv add package1 package2 package3

# 5. Run the project
uv run python main.py
```

### Creating New Project

```bash
# 1. Create and navigate to directory
mkdir my-project
cd my-project

# 2. Initialize UV
uv init

# 3. Add dependencies
uv add requests pandas

# 4. Create script
cat > main.py << 'EOF'
import requests
print("Hello from UV!")
EOF

# 5. Run it
uv run python main.py
```

## How UV Works

### Virtual Environment

- UV automatically creates `.venv/` in your project directory
- You **don't need to manually activate** the venv
- `uv run` automatically uses the project's `.venv`
- Dependencies are installed to `.venv/`

### Dependency Resolution

- Dependencies are defined in `pyproject.toml`
- Lock file `uv.lock` ensures reproducible installs
- UV resolves dependencies faster than pip
- Handles version conflicts intelligently

## Common Scenarios

### Scenario 1: User wants to run a Python script

```bash
# User says: "Run my script.py"
# Claude should use:
uv run python script.py
```

### Scenario 2: User wants to install a package

```bash
# User says: "Install the requests library"
# Claude should use:
uv add requests
```

### Scenario 3: User wants to run tests

```bash
# User says: "Run the tests"
# Claude should use:
uv run pytest
```

### Scenario 4: User wants to start Jupyter

```bash
# User says: "Start Jupyter Lab"
# Claude should:
uv add --dev jupyter  # If not already added
uv run jupyter lab
```

### Scenario 5: User has requirements.txt

```bash
# User says: "Install dependencies from requirements.txt"
# Claude should use:
cat requirements.txt | xargs -I {} uv add {}
# Or for dev dependencies:
cat requirements-dev.txt | xargs -I {} uv add --dev {}
```

### Scenario 6: User wants to use a tool temporarily

```bash
# User says: "Format my code with black"
# Claude should use:
uvx black .
# Not: uv add black (unless they want it permanent)
```

## Important Rules for Claude

1. **Always prefer `uv run` over plain commands** when executing Python code
2. **Always use `uv add` over `pip install`** when installing dependencies
3. **Work with projects in place** - don't require migration to specific directory
4. **Create pyproject.toml if missing** using `uv init`
5. **Use `uvx` for one-off tools** (formatters, linters) unless user wants them permanent
6. **Check for existing pyproject.toml** before running `uv init`
7. **Use `uv sync` after manually editing pyproject.toml**

## pyproject.toml Structure

After `uv init`, the file looks like:

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "Add your description here"
requires-python = ">=3.11"
dependencies = []

[tool.uv]
dev-dependencies = []
```

When you add dependencies with `uv add requests`:

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "Add your description here"
requires-python = ">=3.11"
dependencies = [
    "requests>=2.31.0",
]

[tool.uv]
dev-dependencies = []
```

## Troubleshooting

### "Command not found: uv"

UV is not installed. Install with:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### "No pyproject.toml found"

Initialize UV in the project:
```bash
uv init
```

### "Module not found" errors

Sync dependencies:
```bash
uv sync
```

### Dependencies not updating

Remove lock file and resync:
```bash
rm uv.lock
uv sync
```

### Want to see what's installed

```bash
uv tree  # Show dependency tree
uv pip list  # List installed packages
```

## Quick Reference

```bash
# Setup
uv init                          # Initialize project
uv add <package>                 # Add dependency
uv sync                          # Install dependencies

# Run
uv run python script.py          # Run script
uv run pytest                    # Run tests
uv run jupyter lab               # Start Jupyter

# Temporary tools
uvx black .                      # Run without installing
uvx ruff check .                 # Run linter

# Info
uv tree                          # Show dependencies
uv pip list                      # List packages
```

## When NOT to Use UV

- User explicitly asks for virtual environment activation
- User is working in a specific activated environment already
- User explicitly uses `pip` or `conda` in their question
- System-level Python operations

In these cases, respect the user's explicit choices.
