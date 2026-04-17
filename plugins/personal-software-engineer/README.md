# Personal Software Engineer

Personal skills collection for software engineering work.

## Installation

```bash
claude plugin install personal-software-engineer
```

## Skills

No skills yet. Add SKILL.md files under `skills/<skill-name>/` to grow this collection.

## Adding a Skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter:

```markdown
---
name: <skill-name>
description: <trigger description with examples>
---

# Skill Title

Skill content here.
```

2. Run validation:

```bash
./scripts/validate-plugin-structure.sh personal-software-engineer
```

3. Update `plugin.json` skills array and bump version:

```bash
echo "y" | ./scripts/bump-plugin-version.sh personal-software-engineer <new-version>
```
