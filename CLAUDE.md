# Watchlist Brief

## What this is

A single-page "Monday morning brief" for a small stock watchlist, published on GitHub Pages
and refreshed by an agent. For each company it shows the last five trading days as a
sparkline, the week's move, what happened in the news in the last seven days, and what is
on the calendar for the next two weeks (earnings, product events, Fed decisions, and so on).

- The entire site is `index.html`. Nothing else is served.
- No server, no build step, no API key, no runtime data fetching, no external files.
  All numbers are typed into the page by the refresh agent at refresh time.
- Unofficial and educational. Not investment advice, and the page says so.

## Rules that never change

1. All CSS lives in one `<style>` in `<head>`; all JS lives in one `<script>` at the end of `<body>`.
2. Never link to local files (no `style.css`, no `app.js`, no images, no second HTML page).
3. All data lives in three objects at the top of the `<script>` block, in this order:
   - `WATCHLIST`: the tickers being followed and why. Edited by the owner only.
   - `BRIEF`: everything the refresh agent found (prices, news, calendar), plus `asOf`,
     `retrievedAt`, and `mode` (`"sample"` or `"live"`).
   - `CHANGELOG`: one entry per refresh, newest first, each with a date and a list of
     one-line summaries of what moved.
   Rendering code only reads from these three objects.
4. A refresh changes `BRIEF` and `CHANGELOG` only. It never touches `WATCHLIST`, CSS, or
   rendering code.
5. Never invent a number, a headline, a date, or a URL. Every price has a source URL.
   Every news item and calendar item has a source URL and a date. If something cannot be
   found, record it as missing (`null` or an empty list) and say so in the changelog entry.
   The page must render correctly with missing data.
6. Weekly change is computed by the page from the closes in `BRIEF`; never type a percentage.
7. The page must work on a phone (readable at 380px, no horizontal scroll, tap targets at
   least 44px) and print on one or two pages as a clean brief.

## How work is done here

- **Changing how the page looks or works**: follow the `dashboard-page` skill
  (`.claude/skills/dashboard-page/SKILL.md`).
- **Refreshing the numbers and the news**: use the `refresh-brief` agent, which follows the
  `refresh-brief` skill (`.claude/skills/refresh-brief/SKILL.md`).
- **Before finishing any task**, run the `qa-reviewer` agent on `index.html` and fix what it
  reports. If an agent cannot launch another agent, the main session runs `qa-reviewer`
  after that agent returns.
- The owner publishes. Claude edits files; the owner commits and syncs from VS Code after
  reading the diff.
