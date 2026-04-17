# Changelog

All notable changes to the Atlassian Tools plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.2.3] - 2026-04-17

### Changed

- Ported from bitwarden/ai-plugins — removed Bitwarden-specific branding and references
- Renamed MCP server package from `@bitwarden/atlassian-tools-mcp` to `@nguyenthanhson/atlassian-tools-mcp`
- Updated MCP tool prefix from `mcp__bitwarden-atlassian__` to `mcp__atlassian__`

## [2.2.2] - 2026-04-15

### Fixed

- Removed invalid `view` option from `bodyFormat` parameter in `get_confluence_page_comments`

## [2.2.1] - 2026-04-14

### Security

- Update a dependency vulnerability

## [2.2.0] - 2026-04-03

### Added

- `get_issue_remote_links` MCP tool for fetching remote links attached to a Jira issue
- `researching-jira-issues` skill for deep Jira issue reads with linked issue traversal and Confluence follow-through

## [2.1.0] - 2026-03-20

### Added

- `get_issue` now includes populated custom fields in an "Additional Fields" section with human-readable field names

## [2.0.0] - 2026-03-09

### Changed

- Migrated from direct site URLs to Atlassian API gateway (`api.atlassian.com`) for all API requests
- Replaced `ATLASSIAN_JIRA_URL` and `ATLASSIAN_CONFLUENCE_URL` env vars with single `ATLASSIAN_CLOUD_ID`

## [1.1.0] - 2026-03-07

### Added

- Confluence client layer with 6 Confluence MCP tools
- `download_attachment` tool for Jira attachments

## [1.0.0] - 2026-02-23

### Added

- Custom MCP server with 4 read-only Jira tools
