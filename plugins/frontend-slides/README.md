# frontend-slides

Create stunning, zero-dependency HTML presentations from scratch or by converting PowerPoint files — with a "show, don't tell" style discovery approach.

> Originally created by [Zara (@zarazhangrui)](https://github.com/zarazhangrui/frontend-slides). Packaged for this marketplace.

## What It Does

Instead of asking you to describe design preferences in words, it generates visual style previews and lets you pick what looks right. Output is a single self-contained HTML file — no npm, no frameworks, no build step.

Features:

- **Visual style discovery** — Pick from 3 generated previews based on mood, or choose from 12 curated presets
- **PPT conversion** — Convert `.pptx` files to web, preserving all images and content
- **Zero dependencies** — Single HTML file with inline CSS/JS, works offline
- **Anti-AI-slop** — Distinctive styles that avoid generic aesthetics (no purple gradients on white)
- **Deploy & export** — Share via a live Vercel URL or export to PDF

## Install

```bash
claude plugin add frontend-slides github:nguyenthanhson/ai-plugins
```

## Usage

Trigger phrases (say any of these in Claude Code):

- "Create a pitch deck for my startup"
- "Make slides for my conference talk"
- "Convert my presentation.pptx to a web slideshow"
- "Build me an HTML presentation about X"

## Included Styles

**Dark:** Bold Signal, Electric Studio, Creative Voltage, Dark Botanical, Neon Cyber, Terminal Green  
**Light:** Notebook Tabs, Pastel Geometry, Split Pastel, Vintage Editorial, Swiss Modern, Paper & Ink

## Output

A single self-contained HTML file:

```
my-presentation.html   ← open in browser, no setup needed
```

Navigation: arrow keys, Space, scroll/swipe, or click nav dots.

Optional: deploy to a shareable Vercel URL (`bash scripts/deploy.sh`) or export to PDF (`bash scripts/export-pdf.sh`).

## Skills

| Skill | Description |
|---|---|
| `frontend-slides` | Creates animation-rich HTML presentations from scratch or PPT conversion |

## License & Attribution

This plugin packages the [frontend-slides](https://github.com/zarazhangrui/frontend-slides) skill by [Zara (@zarazhangrui)](https://github.com/zarazhangrui). All skill content and reference files belong to the original author.
