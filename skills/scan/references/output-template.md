# Output Template — Digest Format

Use this template for all `/catchup` output. Follow it exactly.

## Header

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  CATCHUP — [today's date]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Per-Source Section (success)

```
## [Source Name]
  [version or date]
  + [Change description 1]
  + [Change description 2]

  [older version or date]
  + [Change description 3]
```

**Rules:**
- Use `##` for each product name
- Indent version/date and bullet points with 2 spaces
- Use `+` prefix for each change item
- Maximum 3 most recent entries per source
- Maximum 10 bullet points total per source
- Present sources in the order they appear in the config

## Per-Source Section (failure)

```
## [Source Name]
  ⚠ Could not fetch — [error reason]
  → Visit: [url]
```

**Rules:**
- Show `⚠` with the specific error (403, timeout, network error, etc.)
- Always include the direct URL so the user can visit manually

## Footer

```
─────────────────────────────────────────────
  [N] sources · [M] failed
  /catchup --list · --add · --remove
─────────────────────────────────────────────
```

- Use thick line (`━`) for the top header, thin line (`─`) for the footer
- Show total fetched and failed counts
- Show quick action hints

## All Sources Failed

If every source fails, skip the digest format and output:

```
⚠ All [N] sources failed to fetch. Check your network connectivity or try again later.
Run /catchup --list to review your configured sources.
```

## Full Example

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  CATCHUP — March 29, 2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Claude Code
  v1.0.30 — Mar 25
  + Multi-file editing support
  + Fixed terminal rendering on Windows
  + New /compact command for context management

  v1.0.29 — Mar 20
  + Added streaming diff view
  + Improved MCP server reconnection

## GitHub Copilot
  ⚠ Could not fetch — 403 Forbidden
  → Visit: https://github.com/github/copilot-cli/releases

## OpenAI Codex
  Mar 27
  + Codex CLI now supports background tasks
  + New model codex-mini-latest available

## OpenClaw
  v0.5.0 — Mar 26
  + New plugin system for custom extensions
  + Improved multi-agent orchestration

─────────────────────────────────────────────
  4 sources · 1 failed
  /catchup --list · --add · --remove
─────────────────────────────────────────────
```
