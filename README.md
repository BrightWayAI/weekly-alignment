# Team Alignment Scanner

A Claude Code plugin that monitors your Slack channels for cross-team misalignment — duplicate work, conflicting decisions, and coordination gaps.

## What It Does

If you manage across multiple teams, you're in a dozen Slack channels and your reports are each in a dozen different ones. The overlaps and conflicts between those channels are where things go wrong — two teams start solving the same problem, or one team makes a decision that contradicts something another team is already building.

This plugin hands that job to Claude.

## Skills

| Skill | Command | What it does |
|-------|---------|-------------|
| **scan** | `run my weekly alignment check` | Full weekly scan — reads all configured Slack channels, cross-references activity, flags conflicts. Produces a prioritized brief with specific actions. |
| **daily-pulse** | `daily pulse` / `what did I miss` | Quick 24-hour skim. Surfaces anything urgent or noteworthy without full cross-team analysis. |
| **report** | `dig into [issue]` | Deep dive into a specific conflict. Goes back 14 days, traces the timeline, recommends a concrete next step. |
| **update-risks** | `update risks` / `update config` | Quickly update any part of your scanner config — risks, channels, teams, patterns, delivery preferences — without re-running the full setup. |

`scan`, `daily-pulse`, and `report` each delegate their Slack-reading and synthesis to the `alignment-scanner` subagent (mode-dispatched: `scan` / `pulse` / `report`) — the skill itself just handles pre-flight checks, delivery, and history.

## What It Detects

- **Duplicate work** — two teams building the same thing without knowing
- **Conflicting decisions** — choices made in one channel that contradict another
- **Unaware stakeholders** — decisions that affect teams who weren't in the room
- **Resource conflicts** — same person or team pulled in multiple directions
- **Dependency gaps** — one team blocked on work another hasn't started
- **Timeline mismatches** — teams assuming different deadlines for the same thing
- **Scope creep across teams** — one team's expanding scope eating into another's territory
- **Communication dead zones** — decisions made in DMs that never surface

Plus whatever org-specific patterns you describe during setup.

## Setup

### Prerequisites

- [Claude Code](https://claude.ai/download) (CLI, desktop app, or IDE extension)
- A Slack MCP server connected to your workspace ([setup guide](https://modelcontextprotocol.io))

### Install

```
/plugin marketplace add BrightWayAI/nucleus
/plugin install alignment@nucleus
```

Or copy this `alignment` folder into your Claude Code plugins directory.

### First Run

Just say `run my weekly alignment check`. If you haven't set up yet, the scanner will automatically start the setup interview — no extra step needed.

Or run setup explicitly:
```
/alignment-setup
```

Setup takes ~5 minutes. Claude pulls your Slack channels live from the API (so you pick from a list instead of typing names), asks about your teams, and captures what kinds of misalignment happen in your org. Your answers get saved so the interview doesn't repeat.

### Schedule It

```
/schedule
```

Set the scan to run every Monday morning. Now you start each week with a brief on what's misaligned before it becomes a problem.

## Example Output

```
WEEKLY ALIGNMENT BRIEF — Week of March 3–7

TL;DR: Two significant conflicts found. Platform and Product are building
overlapping caching solutions. Data team's schema migration timeline
conflicts with the Q2 feature launch.

HIGH PRIORITY

### Duplicate Caching Work
Teams involved: Platform, Product
What's happening: In #platform-eng on Tuesday, @alice said they're building
a Redis cache layer for API responses. In #product-dev on Wednesday, @bob
said they're evaluating caching options and leaning toward Memcached.
Neither thread references the other.
Why it matters: Two teams will build, test, and maintain separate caching
infrastructure for overlapping use cases.
Suggested action: Get Platform and Product leads in a room this week to
decide who owns caching.
```

## Customization

Run `/alignment-setup` again anytime your org changes — new teams, new channels, new risks.

For quick updates to what you're tracking (without re-running the full interview), use `/alignment-update-config`.

<!-- OPENAI-SUPPORT:START -->
## ChatGPT and Codex

Team Alignment ships as a native OpenAI plugin as well as a Claude plugin. In
ChatGPT desktop Local Work, enable **Team Alignment** and ask naturally or mention
`@Team Alignment`. In Codex, use natural language or the namespaced skills exposed
by the plugin. Claude slash-command names in this README remain workflow aliases.

All hosts resolve the same `<config-root>` used by Cortex, so Claude, ChatGPT desktop,
and Codex can share identity, voice, memory, and per-plugin settings without copying
them. The installed plugin directory is read-only at runtime. See
[`references/openai-portability.md`](references/openai-portability.md) for capability
mapping, connector checks, permissions, and honest degraded behavior.

Import the full catalog from
[`BrightWayAI/nucleus`](https://github.com/BrightWayAI/nucleus); Nucleus is the master
marketplace, while each plugin remains independently installable.
<!-- OPENAI-SUPPORT:END -->


## License

MIT — use it, modify it, share it.
