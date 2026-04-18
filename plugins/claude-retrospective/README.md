# Claude Retrospective

Comprehensive analysis of Claude Code sessions to identify successful patterns, problematic areas, and opportunities for improvement through multi-source data collection and evidence-based insights.

Adapted from [bitwarden/ai-plugins](https://github.com/bitwarden/ai-plugins/tree/main/plugins/claude-retrospective) v1.1.1.

## Skills

### retrospecting

Performs comprehensive session analysis by examining git history, conversation logs, code changes, and user feedback. Generates structured retrospective reports (quick or comprehensive) with actionable recommendations. Suggests improvements to `.claude/CLAUDE.md` and skill files based on findings.

**Trigger phrases**: "do a retrospective", "how did that session go?", "analyze the last X hours", "what could we improve?"

### extracting-session-data

Locates, lists, filters, and extracts structured data from Claude Code native session logs at `~/.claude/projects/{project-dir}/{session-id}.jsonl`. Supports both single and multi-session analysis via four bash scripts:

- `locate-logs.sh` — resolve log directory path for a project
- `list-sessions.sh` — enumerate sessions with metadata (size, lines, date, branch)
- `extract-data.sh` — parse JSONL logs by type (metadata, errors, tool-usage, statistics, etc.)
- `filter-sessions.sh` — filter sessions by date range, branch, size, error presence, or keyword

### analyzing-git-sessions

Analyzes git commits and changes within a timeframe or commit range, providing structured summaries (concise / detailed / code-review format) for retrospectives or work logs.

## Dependencies

- `bash` v4.0+
- `jq` — install with `brew install jq` (macOS) or `apt-get install jq` (Linux)

## Report Storage

Reports are saved to `${CLAUDE_PROJECT_DIR}/.claude/skills/retrospecting/reports/YYYY-MM-DD-description-SESSION_ID.md`.
