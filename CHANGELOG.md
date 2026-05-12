# Changelog

All notable changes to weekly-alignment are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/). Versions match `plugin.json`.

## [1.4.0] — Config-root refactor

### Changed
- **Plugin config + scan history moved to a user-chosen folder.** Previously used `${CLAUDE_PLUGIN_DATA}/references/org-context.md` and `${CLAUDE_PLUGIN_DATA}/history/`, which resolved inside the plugin's source folder (read-only under Cowork). Now lives at:
  - `<config-root>/plugins/weekly-alignment.org-context.md` (org-context replaces former references path)
  - `<config-root>/plugins/weekly-alignment.history/` (scan + pulse + report history)
- **Setup gets a Step 0 config-root bootstrap** before the Slack-connection check (renamed to Step 0b).
- **All operating skills** (`scan`, `daily-pulse`, `report`, `update-risks`) updated to read/write the new paths.
- **User-facing prompts debranded** for fork-friendliness.

## [1.3.0] — Initial published release

### State
- Five skills shipping: `setup`, `scan`, `daily-pulse`, `report`, `update-risks`.
- `/scan` produces the weekly cross-team alignment brief (Monday morning).
- `/daily-pulse` provides a lighter daily check across monitored Slack channels.
- `/report` runs a deep-dive on a specific cross-team conflict or alignment issue.
- `/update-risks` quickly updates risks, tensions, teams, channels, delivery preferences, or watch patterns without re-running full setup.
- `/setup` interviews the user about org structure, picks Slack channels to monitor, and captures cross-team misalignment patterns to watch for.
- All scan history (findings, pulses, reports) stays local in `history/` (gitignored).
- Org-specific user context stored at `references/org-context.md` (gitignored).

### Notes
- Pre-1.3 history was tracked outside this repo; 1.3.0 is the canonical baseline going forward.
- Future entries will follow the standard Keep-a-Changelog format (Added / Changed / Fixed / Removed / Deprecated / Security).
