# ai-plugins

Personal Claude Code plugin marketplace for professional work.

## Plugins

| Plugin | Version | Description |
| --- | --- | --- |
| [atlassian-tools](./plugins/atlassian-tools) | 2.2.3 | Read-only Jira/Confluence access via MCP with deep issue research skill |
| [personal-software-engineer](./plugins/personal-software-engineer) | 0.2.0 | UX expert, devil's advocate, and software engineering skills |
| [playwright-typescript-code-review](./plugins/playwright-typescript-code-review) | 0.2.0 | Repository-aware Playwright TypeScript code reviewer. Discovers local coding standards before reviewing diffs for reliability, maintainability, and correctness. |
| [claude-retrospective](./plugins/claude-retrospective) | 1.1.1 | Comprehensive analysis of Claude Code sessions to identify successful patterns, problematic areas, and opportunities for improvement. |
| [codebase-to-course](./plugins/codebase-to-course) | 0.1.0 | Turn any codebase into a beautiful, interactive HTML course for non-technical vibe coders. |
| [frontend-slides](./plugins/frontend-slides) | 0.1.0 | Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoint files. |

## Usage

### Adding this marketplace to Claude Code

```bash
# Short form (GitHub owner/repo)
/plugin marketplace add nguyenthanhson/ai-plugins

# Full GitHub URL
/plugin marketplace add https://github.com/nguyenthanhson/ai-plugins
```

After adding the marketplace, restart Claude Code for the changes to take effect.

You can also use `/plugin` interactively to manage marketplaces and plugins through a guided interface.

### Installing plugins

Once the marketplace is added, install plugins using:

```bash
/plugin install atlassian-tools@nguyenthanhson-ai-plugins
/plugin install personal-software-engineer@nguyenthanhson-ai-plugins
```

Plugins are installed to `~/.claude/plugins/` by default. Restart Claude Code after installing for the plugin to become active.

### Keeping plugins up to date

Third-party marketplaces don't auto-update by default. To enable automatic updates, open `/plugin`, go to **Marketplaces**, select this marketplace, and choose **Enable auto-update**. Claude Code will then refresh marketplace data and update installed plugins at startup.

You can also update manually at any time:

```bash
/plugin marketplace update nguyenthanhson-ai-plugins
```

### Uninstalling

```bash
/plugin remove atlassian-tools
/plugin remove personal-software-engineer
```

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
