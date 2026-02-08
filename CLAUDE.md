# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Agent Skills is an open format for giving agents new capabilities and expertise. This repository contains:
- **Specification and documentation** for the Agent Skills format
- **Reference SDK** (`skills-ref/`) - Python library for working with skills
- **Documentation website** content and examples

Skills are folders containing instructions, scripts, and resources that agents can discover and use. The format enables "write once, use everywhere" agent capabilities.

## Development Setup

This project uses Python 3.11+ with either pip or uv for dependency management.

### Installation (skills-ref)

```bash
cd skills-ref
uv sync  # or python -m venv .venv && source .venv/bin/activate && pip install -e .
source .venv/bin/activate  # (.venv\Scripts\Activate.ps1 on Windows)
```

### Common Commands

```bash
# Validate a skill directory
skills-ref validate path/to/skill

# Read skill properties (outputs JSON)
skills-ref read-properties path/to/skill

# Generate agent prompt XML for skills
skills-ref to-prompt path/to/skill-a path/to/skill-b

# Run tests
uv run pytest  # or pytest

# Format and lint code
uv run ruff format .
uv run ruff check --fix .

# Run a single test
uv run pytest tests/test_parser.py::test_function_name
```

## Architecture

### Key Components

- **`skills_ref.parser`**: Parses skill.yaml metadata files using strictyaml
- **`skills_ref.validator`**: Validates skill directory structure and content
- **`skills_ref.prompt`**: Generates XML prompt format for agent integration
- **`skills_ref.models`**: Data models for skill properties and validation results
- **`skills_ref.cli`**: Command-line interface built with click

### Skill Format

Skills follow a standardized directory structure:
- `skill.yaml` - Metadata (name, description, triggers, etc.)
- `SKILL.md` - Main instructions for the agent
- Optional resources (scripts, examples, documentation)

The reference library validates this format and generates XML prompts that agents can consume.

### Integration Pattern

The `to_prompt()` function generates `<available_skills>` XML blocks for agent system prompts:

```xml
<available_skills>
<skill>
<name>skill-name</name>
<description>What this skill does and when to use it</description>
<location>/path/to/skill/SKILL.md</location>
</skill>
</available_skills>
```

## Testing

Tests are located in `skills-ref/tests/` and cover:
- **Parser tests**: YAML parsing and validation
- **Validator tests**: Skill directory structure validation
- **Prompt tests**: XML generation for agent integration

## Repository Structure

- `/docs/` - Documentation and website content
- `/skills-ref/` - Python reference implementation
  - `src/skills_ref/` - Core library code
  - `tests/` - Test suite
- Root level contains project documentation (README, LICENSE)

## Important Notes

- The skills-ref library is for **demonstration purposes only**, not production use
- Supports cross-platform development (macOS, Linux, Windows)
- Uses Apache 2.0 license for code, CC-BY-4.0 for documentation
- Emphasizes community contribution and standardization