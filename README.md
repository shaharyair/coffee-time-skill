# Coffee Time ☕

An agent skill for [Claude Code](https://claude.com/claude-code) (and any tool that reads `SKILL.md`). Go get coffee. The agent keeps working without bugging you.

## What it does

Normally an agent stops to ask: which approach? can I write this file? is this okay? That's fine when you're sitting there, useless when you've stepped away. **Coffee Time flips the default: when the agent would ask, it decides instead.** It picks a sensible, reversible default, logs the decision, and keeps moving. It only freezes for the genuinely dangerous stuff (force-pushing main, DB migrations, spending money, sending email, deploying to prod).

When you come back, it leads with a `COFFEE-LOG.md`: what got done, the decisions worth a glance, and anything it parked for you.

## Install

```bash
npx skills add shaharyair/coffee-time-skill
```

Or copy `skills/coffee-time/SKILL.md` into your `~/.claude/skills/coffee-time/`.

## Use

```
/coffee-time              # finish whatever you're already working on, autonomously
/coffee-time <a backlog>  # drain a list: Jira filter, TODO.md, your todos
```

### One catch you have to do yourself

A skill controls the agent's *behavior*, not the harness. It **cannot** turn off permission prompts. So the first thing it does is tell you:

> Hit **Shift+Tab** until it says `accept edits on`, then go enjoy.

Without that, the agent still freezes on the first file write asking permission. Brain (skill) plus hands (acceptEdits) plus legs (its task list). You flip the one switch, it does the rest.

## What it will NOT do

Autonomy means *not asking*, not *cutting corners*. It still follows your project rules (tests, docs, commit hygiene), and it hard-stops on anything irreversible, costly, or outward-facing. If anything, it's more careful, because nobody's watching.

## License

MIT
