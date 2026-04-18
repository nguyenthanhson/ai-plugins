# ai-plugins

Personal Claude Code plugin marketplace for professional work.

## Plugins

| Plugin | Version | Description |
| --- | --- | --- |
| [atlassian-tools](./plugins/atlassian-tools) | 2.2.3 | Read-only Jira/Confluence access via MCP with deep issue research skill |
| [personal-software-engineer](./plugins/personal-software-engineer) | 0.2.0 | UX expert, devil's advocate, and software engineering skills |
| [playwright-typescript-code-review](./plugins/playwright-typescript-code-review) | 0.1.0 | Repository-aware Playwright TypeScript code reviewer. Discovers local coding standards before reviewing diffs for reliability, maintainability, and correctness. |

## Installing Plugins

### Prerequisites

- [Claude Code](https://claude.ai/code) CLI installed

### Via plugin marketplace (recommended)

1. **Register this marketplace:**
   ```bash
   claude plugin add marketplace github:nguyenthanhson/ai-plugins
   ```

2. **Install a plugin:**
   ```bash
   claude plugin install atlassian-tools
   claude plugin install personal-software-engineer
   ```

3. **Restart Claude Code** to discover the new skills.

### Via git (manual)

1. **Clone the repo:**
   ```bash
   git clone https://github.com/nguyenthanhson/ai-plugins.git ~/.claude/ai-plugins
   ```

2. **Symlink the plugin you want:**
   ```bash
   mkdir -p ~/.claude/plugins
   ln -s ~/.claude/ai-plugins/plugins/personal-software-engineer ~/.claude/plugins/personal-software-engineer
   ln -s ~/.claude/ai-plugins/plugins/atlassian-tools ~/.claude/plugins/atlassian-tools
   ```

3. **Restart Claude Code.**

### Verify

```bash
claude plugin list
```

You should see the installed plugins with their skill names.

### OpenAI Codex CLI

Follow the instructions in [`.codex/INSTALL.md`](./.codex/INSTALL.md).

```bash
git clone https://github.com/nguyenthanhson/ai-plugins.git ~/.codex/ai-plugins
mkdir -p ~/.agents/skills
ln -s ~/.codex/ai-plugins/plugins/atlassian-tools/skills ~/.agents/skills/atlassian-tools
ln -s ~/.codex/ai-plugins/plugins/personal-software-engineer/skills ~/.agents/skills/personal-software-engineer
```

Restart Codex to discover the skills.

## Updating

### Via plugin marketplace

```bash
claude plugin update atlassian-tools
claude plugin update personal-software-engineer
```

### Via git

```bash
cd ~/.claude/ai-plugins && git pull
```

Skills update instantly through the symlink.

## Uninstalling

```bash
claude plugin remove atlassian-tools
claude plugin remove personal-software-engineer
```

If installed manually: `rm ~/.claude/plugins/<plugin-name>`

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
