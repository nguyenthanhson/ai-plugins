# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A personal Claude Code plugin marketplace. Plugins are distributed via `claude plugin add marketplace github:nguyenthanhson/ai-plugins` and installed individually. Each plugin lives in `plugins/<name>/` and is self-contained.

## Commands

### Validation (run after any plugin change)

```bash
# Validate one plugin's structure and plugin.json
./scripts/validate-plugin-structure.sh <plugin-name>

# Validate marketplace.json consistency
./scripts/validate-marketplace.sh

# Validate all plugins at once
./scripts/validate-plugin-structure.sh
```

### Version bumping

```bash
# Updates plugin.json, marketplace.json, README.md table in one step
echo "y" | ./scripts/bump-plugin-version.sh <plugin-name> <new-version>
```

### MCP server (atlassian-tools only)

```bash
cd plugins/atlassian-tools/mcp/atlassian-mcp-server
npm install        # first time
npm run build      # compile TypeScript → build/
npm test           # vitest run
npm run test:watch # vitest watch mode
npm run inspector  # launch MCP inspector for local debugging
```

## Architecture

```
.claude-plugin/marketplace.json   # Registry of all plugins with versions
plugins/
  <plugin-name>/
    .claude-plugin/plugin.json    # name, version, description, author, skills[]
    skills/<skill-name>/SKILL.md  # YAML frontmatter: name, description + skill content
    agents/<agent-name>/AGENT.md  # YAML frontmatter: name, description, tools[]
    mcp/<server-name>/            # Optional TypeScript MCP server (build with tsc)
    README.md
    CHANGELOG.md                  # Must follow Keep a Changelog; version must match plugin.json
scripts/
  validate-plugin-structure.sh   # Validates file structure + SKILL/AGENT frontmatter
  validate-marketplace.sh        # Validates marketplace.json ↔ plugin.json consistency
  bump-plugin-version.sh         # Syncs version across all files
  lib/path-sanitization.sh       # Shared bash helper sourced by validation scripts
```

### Plugin manifest (`plugin.json`) required fields

`name`, `version` (semver X.Y.Z), `description`, `author`. The `skills` array lists relative paths to skill directories and must match directories that exist.

### Skill files (`SKILL.md`) required frontmatter

```yaml
---
name: skill-name
description: Trigger description used by Claude to decide when to invoke this skill
---
```

### Agent files (`AGENT.md`) required frontmatter

```yaml
---
name: agent-name
description: ...
tools:
  - ToolName
---
```

## Adding a New Plugin

1. Create `plugins/<name>/` with the minimum structure (`.claude-plugin/plugin.json`, `README.md`, `CHANGELOG.md`, `skills/<skill>/SKILL.md`)
2. Add an entry to `.claude-plugin/marketplace.json`
3. Run `./scripts/validate-plugin-structure.sh <name> && ./scripts/validate-marketplace.sh`

## Key Constraint

The `atlassian-tools` MCP server is read-only by design — it exposes Jira/Confluence data but never writes. Maintain this constraint when adding new MCP tools.
