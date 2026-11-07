# Ollie Ops Agent — Basic City Beer Co.

Working knowledge base for an AI agent that audits, cleans up and automates the brewery's
[Ollie Ops](https://app.ollieops.com/3950107/dashboard) account.

## What's here

| Path | What it is |
|---|---|
| `reference/ollie_ops_reference.md` | How Ollie Ops works, paraphrased from all 88 articles in Ollie's help center: navigation, data model, workflows, QuickBooks posting behavior, dangerous actions, reports. Items marked **[inferred]** came from video-only articles and need checking in the app. |
| `walkthrough/ui_notes.md` | What the real screens look like, verified in the live account. Wins over the reference when they disagree. |
| `audits/2026-11-07_audit.md` | First full audit of the account, with findings and recommended cleanup order. |

## Ground rules for the agent
1. Read-only by default. Any change in Ollie needs Bart's explicit OK first.
2. Ollie → QuickBooks sync is one-way. Deleting/unreceiving/dumping in Ollie rewrites QBO entries.
3. No accounting-affecting change while QuickBooks is disconnected.
4. Prefer fixing data at the source (brew log, PO, recipe) over adjustments.
5. Record every change made, and why, in a dated log under `changes/`.

## Status (2026-11-07)
- Audit complete; QuickBooks disconnected — waiting on reconnect before cleanup.
- UI walkthrough partly done (tank menu, Update Volume, Transfer, Packaging).
