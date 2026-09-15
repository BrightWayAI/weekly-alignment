---
name: scan
description: >
  Weekly cross-team alignment scanner. Reads Slack channels configured during setup, identifies overlapping initiatives, conflicting priorities, decisions that affect other teams without their knowledge, and resource conflicts — then delivers a concise Monday morning brief. Use this skill when the user says "run my weekly alignment check", "alignment scan", "weekly alignment", "cross-team check", "what's misaligned", "team alignment brief", or any variation involving checking for cross-team conflicts or overlapping work. Also trigger when a scheduled task invokes this skill.
---

<!-- OPENAI-ADAPTER:START -->
## OpenAI host binding

Before acting, read `../../references/openai-portability.md`. That file translates
host-specific tools, agents, artifacts, scheduling, connectors, and config-root
access for ChatGPT and Codex. It overrides concrete Claude/Cowork tool names only;
the workflow, safety gates, and output contract in this skill remain canonical.
<!-- OPENAI-ADAPTER:END -->


# Weekly Alignment Scanner

You are running a weekly cross-team alignment scan. The read-and-synthesize work is delegated to the `alignment-scanner` agent (`mode: scan`) so it runs in its own context window; this skill handles pre-flight checks, delivery, and history — the side effects the agent doesn't do.

## Pre-Flight Check

### Check Slack Connection

Before anything else, verify that Slack MCP tools are available (look for `slack_read_channel`, `slack_search_channels`, `slack_search_public_and_private`).

**If Slack is NOT connected:** Stop and tell the user:
"I need a Slack connection to run the alignment scan. Add a Slack MCP server to your Claude Code settings (or Cowork workspace), then try again."

Do not proceed without Slack.

### Check Org Context

Read the org context file at:
`<config-root>/plugins/weekly-alignment.org-context.md`

**If the file does not exist at all, or contains `[NOT YET CONFIGURED]` markers:** Tell the user:
"Your alignment scanner hasn't been set up yet. Let's fix that now — I'll ask you a few questions about your teams and channels. Takes about 5 minutes."

Then immediately invoke the Skill tool with skill `weekly-alignment-setup` to start the setup interview. Once setup completes and the org-context file is written, continue with the scan from Step 1 below — do NOT ask the user to re-run the alignment check.

**If the file is configured:** Proceed with the scan using the org context to guide every step.

## Step 1: Delegate to alignment-scanner

Invoke the Task tool with `subagent_type="alignment-scanner"` and `mode: "scan"`. Pass:
- **`org-context`** — the parsed contents of `weekly-alignment.org-context.md`.
- **`history`** (optional) — recent files from `<config-root>/plugins/weekly-alignment.history/`, `.../history/pulses/`, `.../history/reports/`, if any exist.

The agent returns a delivery-ready brief plus a structured findings list (severity, teams, one-line summary).

## Step 2: Deliver the Brief

Based on the delivery preference in the org context:

- **Slack DM:** `slack_send_message` to DM the brief to the user
- **Slack channel:** `slack_send_message` to the specified channel
- **Slack canvas:** `slack_create_canvas` with the brief content
- **In conversation:** present directly if no Slack delivery preference or Slack is unavailable

If Slack delivery is configured but Slack tools are unavailable, present in conversation and note: "I couldn't deliver to Slack — you may need to check your Slack MCP connection."

Always end with a "WHAT'S NEXT" footer: to dig deeper into any finding, "dig into [issue name]"; to update what's tracked, "update risks"; for tomorrow, "daily pulse."

## Step 3: Save to History

After delivering, save a copy to:
`<config-root>/plugins/weekly-alignment.history/[YYYY-MM-DD].md`

The saved file is the full brief content as-is.
