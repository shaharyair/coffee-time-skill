---
name: coffee-time
description: Autonomous work mode — keep working through a task without bothering the user for clarification, and stop when the work is genuinely done. When unsure, pick a sensible default, log the decision, and keep going; only a short hard-stop list (irreversible/costly/outward-facing actions) is allowed to interrupt mid-flight. Use when the user says "coffee time", "coffee mode", "I'm getting coffee", "keep working without me", "go autonomous", "don't bother me", "work on this while I'm away", "autopilot", or any phrasing about working unattended for a while. Takes an optional source: default is the current task; point it at a list (Jira filter, TODO file, todo list) to drain a backlog. Note: this skill sets BEHAVIOR only — it cannot disable permission prompts, so it tells the user to switch to acceptEdits mode on launch.
---

# Coffee Time ☕

The user is stepping away. Keep working. Don't freeze waiting for them. When you'd normally ask a question, **decide, log it, and move on.**

"Don't stop" means **don't stop to bug them mid-task** — not "run forever." When the work is genuinely done (list drained, or the current task is finished and verified), you stop. That's the goal, not a failure: finish, write the summary, and wait for them to come back.

## Hard truth on launch: flip the permission mode yourself? No.

This skill controls *behavior* (decide vs. ask). It **cannot** turn off permission prompts — those are harness-level. If the user is in `default` permission mode, you'll still freeze on the first file write asking permission, which kills coffee mode dead.

So the FIRST thing you do when this skill loads:

> "☕ Coffee time. One thing I can't do myself — flip to **acceptEdits** so I don't freeze on permission prompts. Hit **Shift+Tab** until it says `accept edits on`, then go enjoy. I'll take it from here."

Say it once, then start working immediately. Don't wait for confirmation — if a prompt blocks you later, that's their cue.

## 1. Pick the work source (launch arg)

- **No arg / "this" / "current task"** → keep driving whatever is already in flight. Use the existing TodoWrite list if there is one; otherwise build one from the conversation.
- **A list is named** (Jira filter, a TODO.md path, "my todos", a backlog) → pull items from it into a TodoWrite list and drain top-to-bottom. Skip items already done. For Jira, use the existing jira skills/MCP to fetch; for a file, read it.

Either way: maintain a TodoWrite list so progress is visible when they glance back.

## 2. The core loop

For each task:

1. Mark it `in_progress` in TodoWrite.
2. Do the work. Follow all normal project rules (CLAUDE.md, docs-ship-with-change, separate commits, use `cn()`, no DB migrations, etc. — coffee mode does NOT relax quality or safety rules).
3. Hit a fork in the road? → **decide + log** (see §3). Never stop.
4. Verify your own work the way you normally would (build, tests, render-and-read). Coffee mode means *unattended*, not *unchecked*.
5. Mark `completed`. Next task.

Keep going until the list is empty, you hit a hard-stop (§4), or the user comes back.

## 3. Decide + log (the heart of coffee mode)

When you'd normally ask the user a question:

- Pick the **most sensible default** given the codebase, conventions, and what they've said.
- Append one line to `COFFEE-LOG.md` at the repo root (create it if missing): `- [<task>] Chose X over Y because Z. Reversible: yes/no.`
- Keep working.

Bias toward **reversible** choices. If two paths both work, take the one that's easiest to undo later. The log is so they can review and override when back — make it skimmable, not a novel.

Don't log trivial stuff (variable names, obvious imports). Log real decisions: an approach picked over another, an ambiguous spec resolved, a scope call.

## 4. Hard-stop list — the ONLY things worth cold coffee

Stop and wait for the user **only** for actions that are irreversible, cost money, or go outward. Everything else: decide and proceed.

- Deleting data you didn't create, or `git push --force` to a shared branch / main.
- DB migrations or destructive SQL (project rule: user owns when migrations run).
- Anything that spends money or changes billing/subscriptions for real.
- Sending something outward: email, Slack, a published PR comment, a deploy to prod.
- Committing secrets, or an action you genuinely can't reason about the blast radius of.

When you hit one: park that task, write it under a `## ⛔ Waiting for you` section in `COFFEE-LOG.md`, and **move to the next unblocked task**. One hard-stop doesn't end coffee mode — it just defers one item.

## 5. When they come back

Lead with the log. Short summary:
- What got done (task count).
- Decisions worth a glance (point them at `COFFEE-LOG.md`).
- Anything in `⛔ Waiting for you`.

Then ask what's next. Don't bury the open questions.

## Rules coffee mode does NOT relax

Autonomy is about *not asking*, not about *cutting corners*. Still applies in full: TDD/verification, docs-ship-with-the-change, separate commits for separate fixes, no auto-sitemap, no DB migrations, `cn()` for classes, all CLAUDE.md/AGENTS.md rules. If anything, be *more* careful — nobody's watching.
