# codebase-to-course

Turn any codebase into a beautiful, interactive HTML course that teaches how the code works — for non-technical people who build with AI.

> Originally created by [Zara (@zarazhangrui)](https://github.com/zarazhangrui/codebase-to-course). Packaged for this marketplace.

## What It Does

Point it at a project and get back a self-contained HTML course with:

- **Scroll-based modules** with progress tracking and keyboard navigation
- **Code ↔ Plain English translations** — real code with plain-language explanations side by side
- **Animated visualizations** — data flow animations, group chat between components, architecture diagrams
- **Interactive quizzes** that test application, not memorization
- **Glossary tooltips** on every technical term
- **Warm, distinctive design** — no generic purple-gradient AI look

## Who It's For

**"Vibe coders"** — people who build software by instructing AI in natural language, without a traditional CS background. The course teaches enough to steer AI tools better, detect when AI is wrong, and debug when AI gets stuck.

## Install

```bash
claude plugin add codebase-to-course github:nguyenthanhson/ai-plugins
```

## Usage

Trigger phrases (say any of these in Claude Code):

- "Turn this into a course"
- "Explain this codebase interactively"
- "Make a course from this project"
- "Teach me how this code works"
- "Interactive tutorial from this code"
- "Turn `./my-project` into a course"
- "Make a course from `https://github.com/user/repo`"

## Output

A directory named after the project containing:

```
course-name/
  index.html      ← open this in the browser (no setup needed)
  styles.css
  main.js
  modules/
    01-intro.html
    02-actors.html
    ...
```

## Skills

| Skill | Description |
|---|---|
| `codebase-to-course` | Analyzes a codebase and generates a complete interactive HTML course |

## License & Attribution

This plugin packages the [codebase-to-course](https://github.com/zarazhangrui/codebase-to-course) skill by [Zara (@zarazhangrui)](https://github.com/zarazhangrui). All skill content and reference files belong to the original author.
