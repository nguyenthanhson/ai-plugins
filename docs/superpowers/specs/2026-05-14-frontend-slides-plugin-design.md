# Design: frontend-slides Plugin Packaging

**Date:** 2026-05-14  
**Status:** Approved  
**Original skill:** https://github.com/zarazhangrui/frontend-slides  
**Original author:** Zara ([@zarazhangrui](https://github.com/zarazhangrui))

---

## What We're Building

Package the `frontend-slides` Claude Code skill into this plugin marketplace as `plugins/frontend-slides/`. The skill creates zero-dependency, animation-rich HTML presentations from scratch or by converting PowerPoint files, using a "show, don't tell" style discovery approach.

## Approach: Direct Port (Option A)

Copy all skill files verbatim. No modifications to skill logic. Update README and documentation to match this collection's conventions and credit attribution.

## Key Structural Difference from codebase-to-course

The reference files (`STYLE_PRESETS.md`, `viewport-base.css`, `html-template.md`, `animation-patterns.md`) live at the **same level as `SKILL.md`**, not in a `references/` subfolder. There is also a `scripts/` subdirectory. The `SKILL.md` uses relative paths to all of these, so the co-location must be preserved.

## File Structure

```
plugins/frontend-slides/
  .claude-plugin/
    plugin.json                           # name, version 0.1.0, original author Zara
  skills/
    frontend-slides/
      SKILL.md                            # copied verbatim
      STYLE_PRESETS.md                    # copied verbatim (co-located with SKILL.md)
      viewport-base.css                   # copied verbatim (co-located with SKILL.md)
      html-template.md                    # copied verbatim (co-located with SKILL.md)
      animation-patterns.md               # copied verbatim (co-located with SKILL.md)
      scripts/
        extract-pptx.py                   # copied verbatim
        deploy.sh                         # copied verbatim
        export-pdf.sh                     # copied verbatim
  README.md                               # rewritten for this collection; credits Zara
  CHANGELOG.md                            # 0.1.0 initial packaging
```

## plugin.json

```json
{
  "name": "frontend-slides",
  "version": "0.1.0",
  "description": "Create stunning, animation-rich HTML presentations from scratch or by converting PowerPoint files.",
  "author": {
    "name": "Zara",
    "url": "https://github.com/zarazhangrui"
  },
  "skills": ["./skills/frontend-slides"]
}
```

## marketplace.json

A new entry added pointing to `plugins/frontend-slides/`.

## Validation

```bash
./scripts/validate-plugin-structure.sh frontend-slides
./scripts/validate-marketplace.sh
```

## Out of Scope

- No modifications to any skill file content
- No modifications to scripts
- No new agents or MCP servers
