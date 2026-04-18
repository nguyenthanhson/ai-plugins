# Claude Retrospective Plugin — Adaptation Design

**Date**: 2026-04-18  
**Source**: https://github.com/bitwarden/ai-plugins/tree/main/plugins/claude-retrospective (v1.1.1)  
**Target**: `plugins/claude-retrospective/` in this repository

## Goal

Port the Bitwarden `claude-retrospective` plugin into this personal plugin marketplace with minimal delta from upstream, conforming to local conventions.

## Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Cross-plugin enrichment | Strip entirely | Bitwarden-specific plugins not in this collection |
| Skill scope | All 3 skills | Full fidelity; helpers usable standalone |
| Context/template files | Port verbatim | Proven content; skills reference them by path |
| Skill prose | Verbatim (minimal delta) | Preserves proven behaviour, easy to diff against upstream |

## Files to Create

```
plugins/claude-retrospective/
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   ├── retrospecting/
│   │   ├── SKILL.md                       # source verbatim minus Cross-Plugin Enrichment section
│   │   ├── contexts/session-analytics.md  # verbatim
│   │   ├── templates/retrospective-templates.md  # verbatim
│   │   └── scripts/analyze-session-logs.sh       # verbatim
│   ├── extracting-session-data/
│   │   ├── SKILL.md                       # verbatim
│   │   └── scripts/
│   │       ├── locate-logs.sh
│   │       ├── list-sessions.sh
│   │       ├── extract-data.sh
│   │       └── filter-sessions.sh
│   └── analyzing-git-sessions/
│       ├── SKILL.md                       # verbatim
│       └── contexts/example-outputs.md   # verbatim
├── README.md                              # rewritten for this collection
└── CHANGELOG.md                           # new, Keep a Changelog, v1.1.1
```

## plugin.json Changes

- `author.name`: `Son Nguyen`
- `author.url`: `https://github.com/nguyenthanhson`
- `homepage`: `https://github.com/nguyenthanhson/ai-plugins/tree/main/plugins/claude-retrospective`
- `repository`: `https://github.com/nguyenthanhson/ai-plugins`
- Add `skills` array: `["./skills/retrospecting", "./skills/extracting-session-data", "./skills/analyzing-git-sessions"]`
- Version: `1.1.1` (tracks upstream)

## SKILL.md Changes

**`retrospecting/SKILL.md` only**: Remove the `## Cross-Plugin Enrichment` section (references to `bitwarden-security-engineer` and `bitwarden-code-review` plugins). All other content verbatim.

## Marketplace Registration

Add to `.claude-plugin/marketplace.json`:

```json
{
  "name": "claude-retrospective",
  "source": "./plugins/claude-retrospective",
  "version": "1.1.1",
  "description": "Comprehensive analysis of Claude Code sessions to identify successful patterns, problematic areas, and opportunities for improvement."
}
```

## Validation

```bash
./scripts/validate-plugin-structure.sh claude-retrospective
./scripts/validate-marketplace.sh
```

## Out of Scope

- Rewriting skill prose or logic
- Adding new skills
- Backwards-compatibility with Bitwarden marketplace format
