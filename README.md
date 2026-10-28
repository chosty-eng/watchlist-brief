# Watchlist Brief

An unofficial, single-page Monday morning brief for a five-stock watchlist: last week's
price move for each name, the news that explains it, and what is on the calendar next.
Built for students in SIBC and AIM who want a page they can show a recruiter that is
clearly updated every week.

**Live site:** https://USERNAME.github.io/watchlist-brief/

The whole site is one file, `index.html`. No server, no build step, no API key.

## Refreshing

Open the folder in VS Code, open the Claude Code panel, and send one sentence:

> Use the refresh-brief agent. Refresh the brief for today.

The agent looks up each ticker's last five closes, the last seven days of news, and the next
two weeks of scheduled events, writes them into `BRIEF`, adds a dated entry to `CHANGELOG`,
and runs the QA review. You check the numbers against a source you trust (`EVAL.md` tells
you how), then commit and sync from the Source Control panel.

## Files

| File | What it is |
|---|---|
| `index.html` | The whole website. The only thing GitHub Pages serves. |
| `CLAUDE.md` | The rules every Claude session reads first. |
| `EVAL.md` | The evaluation test set you fill in before you publish. |
| `.claude/skills/dashboard-page/SKILL.md` | How the page is built. |
| `.claude/skills/refresh-brief/SKILL.md` | The exact refresh procedure. |
| `.claude/agents/refresh-brief.md` | The agent that does the refresh. |
| `.claude/agents/qa-reviewer.md` | Read-only reviewer. Reports PASS or FAIL, never edits. |

## Keep the `.claude` folder

The `.claude` folder holds the skills and agents. It is a hidden folder on Mac (press
Cmd+Shift+. in Finder to see it). It must be in the repository, or future sessions will not
know the rules. VS Code shows it in the Explorer and publishes it with everything else.

Not investment advice. Prices and dates are looked up by an AI agent and should be verified
before anyone relies on them.
