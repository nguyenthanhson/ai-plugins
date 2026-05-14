# Design: codebase-to-course Plugin Packaging

**Date:** 2026-05-14  
**Status:** Approved  
**Original skill:** https://github.com/zarazhangrui/codebase-to-course  
**Original author:** Zara ([@zarazhangrui](https://github.com/zarazhangrui))

---

## What We're Building

Package the `codebase-to-course` Claude Code skill into this plugin marketplace as `plugins/codebase-to-course/`. The skill turns any codebase into a beautiful, interactive single-page HTML course targeting "vibe coders" — people who build with AI but lack a traditional CS background.

## Approach: Direct Port (Option A)

Copy all skill files verbatim. No modifications to skill logic. Update README and documentation to match this collection's conventions and credit attribution.

## File Structure

```
plugins/codebase-to-course/
  .claude-plugin/
    plugin.json                          # name, version 0.1.0, original author Zara
  skills/
    codebase-to-course/
      SKILL.md                           # copied verbatim from source repo
      references/
        styles.css                       # static asset — course CSS
        main.js                          # static asset — course JS
        _base.html                       # static asset — HTML shell template
        _footer.html                     # static asset — HTML footer template
        build.sh                         # static asset — assembles index.html
        content-philosophy.md            # doc — writing rules for modules
        design-system.md                 # doc — CSS tokens, typography, colors
        gotchas.md                       # doc — common failure points
        interactive-elements.md          # doc — quiz/animation patterns
        module-brief-template.md         # doc — template for parallel builds
  README.md                              # rewritten for this collection; credits Zara
  CHANGELOG.md                           # 0.1.0 — initial packaging
```

## plugin.json

```json
{
  "name": "codebase-to-course",
  "version": "0.1.0",
  "description": "Turn any codebase into a beautiful, interactive HTML course for non-technical vibe coders.",
  "author": {
    "name": "Zara",
    "url": "https://github.com/zarazhangrui"
  },
  "skills": ["./skills/codebase-to-course"]
}
```

## README Updates

The README will be rewritten (not copied) to:
- Match the tone and format of other plugins in this collection
- Include install instructions via `claude plugin add`
- Link back to the original repo and credit Zara
- List trigger phrases and expected output

## marketplace.json

A new entry added pointing to `plugins/codebase-to-course/`.

## Validation

After creating files, run:
```bash
./scripts/validate-plugin-structure.sh codebase-to-course
./scripts/validate-marketplace.sh
```

## Out of Scope

- No modifications to `SKILL.md` content
- No modifications to any `references/` file content
- No new skills, agents, or MCP servers
