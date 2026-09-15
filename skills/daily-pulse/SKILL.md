---
disable-model-invocation: true
name: daily-pulse
description: >
  Quick daily check across monitored Slack channels. Lighter than the full weekly scan — skims for anything urgent or noteworthy since yesterday. Use when the user says "daily pulse", "quick check", "anything happening today", "daily alignment", "what did I miss", or any variation of a quick cross-team status check.
---

<!-- OPENAI-ADAPTER:START -->
## OpenAI host binding

Before acting, read `../../references/openai-portability.md`. That file translates
host-specific tools, agents, artifacts, scheduling, connectors, and config-root
access for ChatGPT and Codex. It overrides concrete Claude/Cowork tool names only;
the workflow, safety gates, and output contract in this skill remain canonical.
<!-- OPENAI-ADAPTER:END -->


# Daily Pulse Check

You are running a quick daily pulse. The read-and-skim work is delegated to the `alignment-scanner` agent (`mode: pulse`); this skill handles pre-flight, delivery, and history.

## Pre-Flight Check

### Check Slack Connection

Verify that Slack MCP tools are available (`slack_read_channel`, `slack_search_channels`).

**If Slack is NOT connected:** Stop and tell the user:
"I need a Slack connection to run the pulse check. Add a Slack MCP server to your Claude Code settings, then try again."

### Check Org Context

Read the org context file at:
`<config-root>/plugins/alignment.org-context.md`

**If the file does not exist at all, or contains `[NOT YET CONFIGURED]` markers:** Tell the user:
"You haven't set up your alignment scanner yet. Let's do that first."
Then invoke the Skill tool with skill `alignment-setup`. Once complete, continue with the pulse check.

**If configured:** Proceed.

## Step 1: Delegate to alignment-scanner

Invoke the Task tool with `subagent_type="alignment-scanner"` and `mode: "pulse"`. Pass the parsed org context.

## Step 2: Deliver the Pulse

Deliver based on the user's delivery preference from org context. If no preference or in conversation, show it inline.

## Step 3: Save to History

Save a copy of the pulse output to:
`<config-root>/plugins/alignment.history/pulses/[YYYY-MM-DD].md`

## Notes

- This is NOT the weekly scan. `alignment-scanner`'s `mode: pulse` does not do full cross-team analysis.
- If the agent flags something as a possible real conflict, suggest running `/alignment-scan` or a `mode: report` deep dive.
