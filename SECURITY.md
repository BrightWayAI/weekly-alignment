# Security Policy

## What this plugin does with your data

Weekly Alignment scans Slack channels you've configured during `/setup` to identify cross-team misalignments, overlapping initiatives, and conflicting priorities. Read-only against Slack; writes only local history files and reports.

**Reads:**
- **Slack** (specifically the channels configured during `/setup`) — channel messages within the scan window, threaded replies. Only channels you've explicitly listed are scanned.
- **Plugin references** — `references/org-context.md` (your org structure, teams, risk patterns to watch).

**Writes:**
- **Plugin org-context** — `references/org-context.md` (after `/setup`, gitignored).
- **Scan history** — `history/` directory (gitignored — contains scan findings local to your machine only).
- **Pulse history** — `history/pulses/*.md` and `history/reports/*.md` (gitignored).

**Does not:**
- **Send Slack messages.** Strictly read-only on Slack.
- **Modify channel content.** No reactions, replies, threads, or DMs sent automatically.
- **Scan channels not in your configured list.** The setup interview captures explicit consent per channel.
- **Send your scan findings to any server.** Reports stay local in `history/`.

## Where data lives

- All scan findings + configured org context stay local in `references/` and `history/` directories inside the installed plugin (gitignored).
- Slack data is read on-demand via your authorized Slack connector and not cached beyond the plugin's history/reports directory.

## What gets sent off your machine

- Whatever your authorized Slack connector sends when invoked. No additional outbound traffic.

## Supported versions

| Version | Supported |
|---------|-----------|
| 1.x     | Yes       |

## Reporting a vulnerability

Report privately via GitHub Security Advisories:

https://github.com/BrightWayAI/weekly-alignment/security/advisories/new

Do not open a public issue for security concerns. We aim to respond within 5 business days.
