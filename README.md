# Simple UV Workflow for Python Development

A straightforward guide to using UV for Python development in any project, anywhere.

## What is UV?

UV is a fast, modern Python package manager written in Rust by the Astral team (creators of Ruff). It's 10-100x faster than pip and combines the functionality of pip, virtualenv, pyenv, and poetry into one tool.

## Why Use UV?

- **Fast** - 10-100x faster than pip for package operations
- **Simple** - One tool replaces pip, virtualenv, pyenv, poetry
- **Modern** - Built-in lock files for reproducible environments
- **Convenient** - No need to manually activate virtual environments
- **Cross-platform** - Works on Linux, macOS, and Windows

## Installation

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Verify installation:
```bash
uv --version
```

## Basic Workflow

UV works with **any Python project in any location**. You don't need to reorganize your projects or move them to special directories.

### Starting with a New Project

```bash
# 1. Create project directory
mkdir my-project
cd my-project

# 2. Initialize UV (creates pyproject.toml)
uv init

# 3. Add dependencies
uv add requests pandas

# 4. Run your code (no activation needed!)
uv run python script.py
```

### Starting with an Existing Project

If you have an existing project with `requirements.txt`:

```bash
# 1. Navigate to your project
cd /path/to/existing/project

# 2. Initialize UV
uv init

# 3. Add dependencies from requirements.txt
cat requirements.txt | xargs -I {} uv add {}

# 4. Run your code
uv run python main.py
```

## Core Commands

### Dependency Management

```bash
# Add a package
uv add requests

# Add multiple packages
uv add requests pandas numpy

# Add development dependency
uv add --dev pytest black ruff

# Add with version constraint
uv add "requests>=2.31.0"

# Remove a package
uv remove requests

# Install/sync all dependencies from pyproject.toml
uv sync
```

### Running Code

The key insight: **Use `uv run` instead of plain commands**

```bash
# Run Python script
uv run python script.py

# Run Python module
uv run python -m mypackage

# Run tests
uv run pytest

# Start Jupyter
uv add --dev jupyter
uv run jupyter lab

# Run interactive Python
uv run ipython
```

**Why use `uv run`?**
- Automatically uses the project's `.venv`
- No need to manually activate the virtual environment
- Works consistently across all projects

### One-Time Tool Usage

For tools you don't want to install permanently:

```bash
# Format code with black (without installing)
uvx black .

# Check code with ruff
uvx ruff check .

# Type check with mypy
uvx mypy src/

# Run any tool temporarily
uvx <tool-name>
```

## How UV Works

### Virtual Environments

- UV automatically creates `.venv/` in your project directory
- `uv run` automatically uses this environment
- No manual activation needed!
- Add `.venv/` to `.gitignore`

### Dependency Files

- **`pyproject.toml`** - Your project configuration and dependencies
- **`uv.lock`** - Lock file ensuring reproducible installs
- Both should be committed to git

### Python Version Management

```bash
# List available Python versions
uv python list

# Install specific Python version
uv python install 3.11

# Pin Python version for project
uv python pin 3.11
```

## Common Workflows

### Web Scraping Project

```bash
mkdir web-scraper
cd web-scraper
uv init
uv add requests beautifulsoup4
uv run python scraper.py
```

### Data Analysis Project

```bash
mkdir data-analysis
cd data-analysis
uv init
uv add pandas numpy matplotlib jupyter
uv run jupyter lab
```

### API Development

```bash
mkdir my-api
cd my-api
uv init
uv add fastapi uvicorn
uv run uvicorn main:app --reload
```

### Machine Learning Project

```bash
mkdir ml-project
cd ml-project
uv init
uv add scikit-learn pandas numpy
uv add --dev pytest
uv run python train.py
```

## Working with Existing Projects

UV works seamlessly with projects in their current locations:

```bash
# Work on project in ~/projects/
cd ~/projects/my-app
uv init  # If no pyproject.toml
uv add requests
uv run python app.py

# Work on project in ~/work/
cd ~/work/client-project
uv init
uv sync
uv run pytest

# Work on project anywhere
cd /anywhere/my-code
uv init
uv add dependencies
uv run python main.py
```

## Command Translation Guide

Replace traditional Python commands with UV:

| Traditional | UV Command |
|------------|------------|
| `python script.py` | `uv run python script.py` |
| `pip install requests` | `uv add requests` |
| `pip install -r requirements.txt` | `cat requirements.txt \| xargs uv add` |
| `pip uninstall requests` | `uv remove requests` |
| `pytest` | `uv run pytest` |
| `jupyter lab` | `uv run jupyter lab` |
| `black .` | `uvx black .` |
| `source venv/bin/activate` | *not needed with `uv run`* |

## Best Practices

1. **Use `uv run` for everything** - Don't manually activate environments
2. **Use `uvx` for one-off tools** - Formatters, linters, etc.
3. **Commit lock files** - Commit both `pyproject.toml` and `uv.lock`
4. **Pin Python versions** - Use `uv python pin` for reproducibility
5. **Keep projects organized** - But they can live anywhere!

## Troubleshooting

### Command not found: uv

Install UV:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Module not found errors

Sync dependencies:
```bash
uv sync
```

### Wrong Python version

Pin the version you need:
```bash
uv python install 3.11
uv python pin 3.11
uv sync
```

### Dependencies not updating

Remove lock file and resync:
```bash
rm uv.lock
uv sync
```

### Want to see installed packages

```bash
uv pip list
uv tree  # Show dependency tree
```

## AI Assistant Integration

### Claude Code Skill

This repo includes a Claude Code skill that teaches Claude to automatically use UV commands. When installed, Claude will:

- Use `uv run python` instead of plain `python`
- Use `uv add` instead of `pip install`
- Use `uv run pytest` instead of plain `pytest`
- Apply UV workflow to any Python project in any location

#### Installation

```bash
# Create skills directory if it doesn't exist
mkdir -p ~/.claude/skills/

# Copy the skill from this repo
cp -r skills/uv-python ~/.claude/skills/

# Verify installation
ls ~/.claude/skills/uv-python/SKILL.md
```

#### Usage

Once installed, Claude Code will automatically:
- Translate Python commands to UV equivalents
- Use `uv run` for all script execution
- Initialize UV in projects when needed
- Follow the UV workflow described in this guide

#### Skill Details

- **Location**: `skills/uv-python/SKILL.md`
- **Format**: Claude Code skill format with metadata and instructions
- **Scope**: Applies to any Python project in any directory
- **Auto-activates**: Triggers when working with Python code

### Codex Skill

This repo also includes a Codex-compatible version of the UV Python skill for use with Codex AI assistants.

#### Installation

```bash
# Create Codex skills directory if it doesn't exist
mkdir -p ~/.codex/skills/

# Copy the skill from this repo
cp -r skills/codex-uv-python ~/.codex/skills/uv-python

# Verify installation
ls ~/.codex/skills/uv-python/SKILL.md
```

#### Usage

Once installed, Codex will automatically:
- Translate Python commands to UV equivalents
- Use `uv run` for all script execution
- Initialize UV in projects when needed
- Follow the UV workflow described in this guide

#### Skill Details

- **Location**: `skills/codex-uv-python/SKILL.md`
- **Format**: Codex skill format with YAML frontmatter
- **Scope**: Universal Python workflow for any project location
- **Complements**: Works alongside other UV-related skills for workspace-based development

## Quick Reference

```bash
# Project setup
uv init                    # Initialize UV in project
uv add <package>          # Add dependency
uv sync                   # Install all dependencies

# Running code
uv run python script.py   # Run script
uv run pytest             # Run tests
uv run jupyter lab        # Start Jupyter

# Tools (temporary)
uvx black .               # Format code
uvx ruff check .          # Lint code

# Python versions
uv python list            # List versions
uv python install 3.11    # Install version
uv python pin 3.11        # Pin for project

# Information
uv pip list               # List packages
uv tree                   # Show dependency tree
```

## Resources

- [UV Documentation](https://docs.astral.sh/uv/)
- [UV GitHub](https://github.com/astral-sh/uv)
- [UV on PyPI](https://pypi.org/project/uv/)

## Key Takeaway

**The UV workflow is simple:**
1. Navigate to any project directory
2. Run `uv init` (if needed)
3. Add dependencies with `uv add`
4. Run code with `uv run`

No special directory structure required. No manual environment activation. Just fast, modern Python development.
