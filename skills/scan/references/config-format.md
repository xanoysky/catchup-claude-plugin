# Configuration

## Config File

`~/.catchup/catchup.json` — a simple array of sources.

If this file doesn't exist, defaults from [default-sources.json](default-sources.json) are used.

```json
[
  {
    "name": "Claude Code",
    "repo": "anthropics/claude-code",
    "url": "https://github.com/anthropics/claude-code/releases",
    "prompt_hint": "Extract the most recent 3 releases with version numbers, dates, and key changes."
  },
  {
    "name": "Cursor",
    "url": "https://www.cursor.com/changelog"
  }
]
```

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | Display name, also used for filtering (e.g., `/catchup claude`) |
| `url` | Yes | Changelog page URL (used as fallback if `repo` is set) |
| `repo` | No | GitHub `owner/repo` — if set, `gh release list` is tried first |
| `prompt_hint` | No | Extraction hint for pages with unusual structure |

## Commands

### `--add "Name" URL [--repo owner/repo]` — Add a source
```
/catchup --add "Cursor" https://www.cursor.com/changelog
/catchup --add "My Tool" https://github.com/owner/repo/releases --repo owner/repo
```
- Read current config (or defaults if no config exists)
- Append the new source (with optional `repo` field)
- If a source with the same name exists, update its URL and repo
- If the URL matches `github.com/{owner}/{repo}/releases`, auto-detect `repo`
- Write to `~/.catchup/catchup.json`

### `--remove name` — Remove a source
```
/catchup --remove cursor
```
- Read current config
- Remove the source matching the name (case-insensitive)
- Write to `~/.catchup/catchup.json`

### `--list` — Show current sources
```
/catchup --list
```
Display format:
```
Catchup — Sources

1. Claude Code — https://github.com/anthropics/claude-code/releases
2. GitHub Copilot — https://github.com/github/copilot-cli/releases
3. OpenAI Codex — https://github.com/openai/codex/releases
4. OpenClaw — https://github.com/openclaw/openclaw/releases

→ /catchup --add "Name" URL to add
→ /catchup --remove name to remove
```

### `--reset` — Restore defaults
```
/catchup --reset
```
- Delete `~/.catchup/catchup.json` (or overwrite with defaults from [default-sources.json](default-sources.json))
- Confirm what was restored
