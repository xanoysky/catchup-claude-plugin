---
name: scan
description: Fetches recent changelogs from any tools or projects and presents a scannable digest. Use when the user wants to catch up on recent updates, releases, or changelogs from any source. Default sources are Claude Code, GitHub Copilot, OpenAI Codex, and OpenClaw.
argument-hint: "claude,codex | --add \"Cursor\" https://cursor.com/changelog | --remove cursor | --list | --reset"
allowed-tools: Read, Write, Bash(gh release *), bash(gh release *), WebFetch, WebSearch, view, edit, create, web_fetch
---

# Catchup

Fetches and summarizes recent changelogs into a single scannable digest.

## Arguments

| Argument | Example | Action |
|----------|---------|--------|
| *(empty)* | `/catchup` | Fetch all sources |
| names | `/catchup claude,codex` | Fetch only matching sources |
| `--add` | `/catchup --add "Cursor" https://www.cursor.com/changelog` | Add a source |
| `--remove` | `/catchup --remove cursor` | Remove a source |
| `--list` | `/catchup --list` | Show current sources |
| `--reset` | `/catchup --reset` | Restore defaults |

## Workflow

### 1. Handle Management Commands

If `$ARGUMENTS` starts with `--`:
- Read [config-format.md](references/config-format.md) for the workflow of each command
- Write changes to `~/.claude/catchup.json`
- **Stop** — do not fetch changelogs

### 2. Load Sources

1. Read `~/.claude/catchup.json`
2. If missing or invalid, read [default-sources.json](references/default-sources.json)

### 3. Apply Filters

If `$ARGUMENTS` contains comma-separated names (no `--` prefix):
- Split by comma, trim, lowercase each token
- Keep only sources whose `name` (lowercased) contains any token
- If no match, list available source names and stop

### 4. Fetch Changelogs

For each source:

1. **If the source has a `repo` field** (e.g. `"owner/repo"`), or the `url` matches `github.com/{owner}/{repo}/releases`:
   - Run `gh release list --repo {owner/repo} --limit 3` via Bash
   - Then run `gh release view {tag} --repo {owner/repo}` for each tag to get details
   - If `gh` succeeds → use this data, skip steps 2–3
2. Call **WebFetch** with the source `url` and this prompt (prepend the source's `prompt_hint` if present):
   ```
   Extract recent changelog/release entries. Return a structured list:
   ### [version or date]
   - Change description
   Keep it concise — maximum 10 bullet points total across all entries.
   If no recent entries are found, say "No recent changes found."
   ```
3. If WebFetch fails → try **WebSearch** with query: `"[source name] changelog latest release [current year]"`
4. If all methods fail → record the error, **continue** to next source

### 5. Output

Format the results using [output-template.md](references/output-template.md).

## Gotchas

- **NEVER fabricate changelog entries.** Only report what WebFetch/WebSearch actually returns.
- Some sites return 403 — the WebSearch fallback usually finds the info anyway.
- Trim to 3 most recent entries per source if content is excessive.
- `~/.claude/` resolves correctly on macOS, Linux, and Windows.
