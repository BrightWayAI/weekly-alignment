---
name: report
description: >
  Deep dive into a specific cross-team conflict or alignment issue. Use when the user says "dig into", "deep dive", "tell me more about", "investigate the conflict between", "report on", or any variation of wanting a detailed analysis of a specific misalignment — not a broad scan, but a focused investigation of one issue.
---

<!-- OPENAI-ADAPTER:START -->
## OpenAI host binding

Before acting, read `../../references/openai-portability.md`. That file translates
host-specific tools, agents, artifacts, scheduling, connectors, and config-root
access for ChatGPT and Codex. It overrides concrete Claude/Cowork tool names only;
the workflow, safety gates, and output contract in this skill remain canonical.
<!-- OPENAI-ADAPTER:END -->


# Alignment Deep Dive Report

You are producing a focused investigation into one cross-team conflict. The read-deep-and-reconstruct work is delegated to the `alignment-scanner` agent (`mode: report`); this skill handles pre-flight, scoping the issue, delivery, and history.

## Pre-Flight Check

### Check Slack Connection

Verify that Slack MCP tools are available (`slack_read_channel`, `slack_search_channels`, `slack_search_public_and_private`).

**If Slack is NOT connected:** Stop and tell the user:
"I need a Slack connection to investigate this. Add a Slack MCP server to your Claude Code settings, then try again."

### Check Org Context

Read the org context file at:
`<config-root>/plugins/weekly-alignment.org-context.md`

**If the file does not exist at all, or contains `[NOT YET CONFIGURED]` markers:** Tell the user:
"You haven't set up your alignment scanner yet. Let's do that first."
Then invoke the Skill tool with skill `weekly-alignment-setup`.

**If configured:** Proceed.

## Step 1: Scope the Issue

If the user gave a specific issue (e.g., "dig into the caching conflict between Platform and Product"), use that.

If they said something vague (e.g., "investigate the thing from this week's scan"), ask:
"Which issue do you want me to dig into? Give me the teams involved or a short description."

Never pass a vague issue to the agent — always resolve it to a scoped description first.

## Step 2: Delegate to alignment-scanner

Invoke the Task tool with `subagent_type="alignment-scanner"` and `mode: "report"`. Pass the parsed org context and the scoped `issue`.

## Step 3: Deliver the Report

Deliver based on the org context's delivery preferences. For reports, also offer to create a Slack canvas (`slack_create_canvas`) since these are longer documents worth sharing.

## Step 4: Save to History

Save the full report to:
`<config-root>/plugins/weekly-alignment.history/reports/[YYYY-MM-DD]-[short-slug].md`

Where `[short-slug]` is a kebab-case summary of the issue (e.g., `caching-conflict-platform-product`).

## Step 5: Offer to Update Risks

After delivering, offer:
"Want me to add this to your tracked risks so the weekly scan keeps an eye on it? Just say 'yes' and I'll update your config."

If yes, invoke the Skill tool with skill `weekly-alignment-update-config`.
