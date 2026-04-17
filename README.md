# ai-plugins

Personal Claude Code plugin marketplace for professional work.

## Install

```bash
# Add this marketplace to Claude Code
claude plugin add marketplace github:nguyenthanhson/ai-plugins

# Install a plugin
claude plugin install atlassian-tools
claude plugin install personal-software-engineer
```

## Plugins

| Plugin | Version | Description |
| --- | --- | --- |
| [atlassian-tools](./plugins/atlassian-tools) | 2.2.3 | Read-only Jira/Confluence access via MCP with deep issue research skill |
| [personal-software-engineer](./plugins/personal-software-engineer) | 0.1.0 | Personal software engineering skills collection |

## Adding a Plugin

1. Create `plugins/<name>/` with the structure below
2. Add an entry to `.claude-plugin/marketplace.json`
3. Run validation: `./scripts/validate-plugin-structure.sh <name> && ./scripts/validate-marketplace.sh`
4. Push to `main`

### Minimum plugin structure

```
plugins/<name>/
├── .claude-plugin/
│   └── plugin.json      # name, version, description, author, skills[]
├── skills/
│   └── <skill-name>/
│       └── SKILL.md     # frontmatter: name, description
├── README.md
└── CHANGELOG.md
```

## Validation

```bash
# Validate a specific plugin
./scripts/validate-plugin-structure.sh <plugin-name>

# Validate marketplace consistency
./scripts/validate-marketplace.sh

# Bump a plugin version
echo "y" | ./scripts/bump-plugin-version.sh <plugin-name> <new-version>
```
