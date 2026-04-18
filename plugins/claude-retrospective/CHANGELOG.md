# Changelog

All notable changes to the Claude Retrospective plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.1] - 2026-04-18

### Added

- Initial release adapted from [bitwarden/ai-plugins](https://github.com/bitwarden/ai-plugins/tree/main/plugins/claude-retrospective) v1.1.1
- `retrospecting` skill — multi-source session analysis with quick/standard/comprehensive depth modes, configuration improvement suggestions, and context budget management
- `extracting-session-data` skill — JSONL session log parser with four bash scripts (locate-logs, list-sessions, extract-data, filter-sessions)
- `analyzing-git-sessions` skill — git commit and diff analyzer with concise/detailed/code-review output formats
- Context files: `session-analytics.md` (analysis framework), `retrospective-templates.md` (report templates), `example-outputs.md` (git analysis examples)

### Changed

- Updated authorship and repository URLs to `nguyenthanhson/ai-plugins`
- Added `skills` array to `plugin.json` per local plugin schema requirements

### Removed

- Cross-Plugin Enrichment section (Bitwarden-specific plugin hooks not applicable to this collection)
