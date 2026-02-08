# AGENTS.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

Agent Skills is an open format for giving AI agents new capabilities and expertise through standardized skill directories. The repository contains:

- **Documentation site** (`docs/`) - Mintlify-based static site at agentskills.io
- **Reference SDK** (`skills-ref/`) - Python library for validating and working with skills
- **Specification** - Complete format definition for `SKILL.md` files

## Development Commands

### Documentation (Mintlify)

The documentation site uses Mintlify and must be run from the `docs/` directory:

```bash
# Install Mintlify CLI (one-time setup)
npm i -g mint

# Run local development server
cd docs && mint dev

# Update Mintlify CLI if having issues
mint update
```

The dev server runs at `http://localhost:3000`. Navigation is configured in `docs/docs.json`.

### Python SDK (skills-ref)

The reference SDK uses modern Python tooling with `uv` and `ruff`:

```bash
# Setup development environment
cd skills-ref
uv sync
source .venv/bin/activate

# Code formatting and linting
uv run ruff format .
uv run ruff check --fix .

# Run tests
uv run pytest

# Use the CLI tool
skills-ref validate path/to/skill
skills-ref read-properties path/to/skill
skills-ref to-prompt path/to/skill-a path/to/skill-b
```

## Architecture

### Core Data Model

The `SkillProperties` class in `skills-ref/src/skills_ref/models.py` defines the schema for skill frontmatter:

- **Required**: `name` (kebab-case), `description` (what it does + when to use)
- **Optional**: `license`, `compatibility`, `allowed_tools`, `metadata`

### Key Components

- **Parser** (`parser.py`) - Reads `SKILL.md` files and extracts YAML frontmatter
- **Validator** (`validator.py`) - Enforces naming conventions and content constraints
- **Prompt Generator** (`prompt.py`) - Creates XML blocks for agent system prompts
- **CLI** (`cli.py`) - Command-line interface using Click

### Skill Format

Skills are directories containing `SKILL.md` with YAML frontmatter plus Markdown instructions. Optional directories:
- `scripts/` - Executable code agents can run
- `references/` - Additional documentation loaded on demand
- `assets/` - Static resources like templates and data files

The format emphasizes progressive disclosure: metadata (~100 tokens) → instructions (<5000 tokens) → resources (as needed).

### Documentation Structure

Mintlify site navigation in `docs/docs.json` defines the page order. Key pages:
- `home.mdx` - Landing page
- `specification.mdx` - Complete format specification
- `what-are-skills.mdx` - Conceptual introduction
- `integrate-skills.mdx` - Integration guide

## Important Constraints

- Skill names: lowercase alphanumeric + hyphens, 1-64 chars, no leading/trailing/consecutive hyphens
- Descriptions: 1-1024 chars, must explain both what it does and when to use it
- Keep main `SKILL.md` under 500 lines, move detailed content to reference files
- File references should be one level deep from skill root

## Testing Skills

Use the reference library to validate skills against the specification:

```bash
skills-ref validate ./path/to/skill
```

This checks frontmatter validity and naming conventions compliance.