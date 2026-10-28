---
name: refresh-brief
description: The exact procedure for refreshing the watchlist brief with current prices, last week's news, and the next two weeks of scheduled events. Use whenever asked to refresh, update, or roll forward the brief. Changes BRIEF and CHANGELOG only.
---

# Refresh the brief

You are updating a page that a person will verify line by line and then publish under their
own name. A wrong number with a confident tone is the worst outcome. A missing number with
an honest note is fine.

## Step 0. Read before you touch anything

1. Read `CLAUDE.md`.
2. Read `index.html` and locate `WATCHLIST`, `BRIEF`, and `CHANGELOG` at the top of the
   `<script>` block. Note the current `BRIEF.asOf` and `BRIEF.mode`.
3. Note today's date and whether US markets are open right now (regular hours are 9:30 AM
   to 4:00 PM Eastern, Monday to Friday, excluding exchange holidays).

## Step 1. Prices, one ticker at a time

For each entry in `WATCHLIST.tickers`:

1. Get the last five completed trading days of closing prices, oldest first. Preferred
   sources, in order; stop at the first that works:
   - `https://stockanalysis.com/stocks/TICKER/history/` (lower-case ticker, fetch the page)
   - `https://www.google.com/finance/quote/TICKER:EXCHANGE`
   - A web search for `TICKER stock price history` and the first reputable result
     (Nasdaq, Yahoo Finance, MarketWatch, WSJ).
2. If the market is open now, also record the latest intraday price as
   `latest: { date, time, price }` with the time in Eastern and set
   `BRIEF.priceType = "intraday"`. If the market is closed, set `latest: null` and
   `BRIEF.priceType = "close"`.
3. Record the URL you actually used as `priceSource`. Record prices to two decimals.
4. If you cannot get all five closes, record the ones you have (never pad, never estimate)
   and write a line in the changelog entry saying which days are missing and why.

Never compute the weekly change. The page does that.

## Step 2. News, last seven days

For each ticker, find up to three news items dated within the last seven days. Use a web
search such as `COMPANY NAME news` and open the results. For each item record:

- `date` (the article's own date, ISO format)
- `headline` (as published, trimmed if very long)
- `source` (publication name)
- `url` (the article URL you opened; never a search results URL)
- `why`: one sentence on why it matters to the stock, in plain words, no hype

Prefer original reporting (Reuters, Bloomberg, WSJ, FT, AP, CNBC, the company's own press
release) over aggregators. Skip anything older than seven days, anything you did not open,
and anything you cannot date. Three good items beat five weak ones. Zero is acceptable:
record an empty list and note it in the changelog.

## Step 3. Calendar, next fourteen days

For each ticker, look for scheduled events inside the next fourteen days: earnings
dates, investor days, product launches, ex-dividend dates, court or regulatory dates. Also
include one market-wide item if it falls in the window and matters to the list (an FOMC
decision, a CPI release). Search `COMPANY NAME earnings date` and `COMPANY NAME investor
relations events`, and confirm the date on the company IR page or a reputable calendar
before recording it. Each item needs `date`, `event`, `source`, `url`. If a date is
"expected" rather than confirmed, say so in the `event` text.

## Step 4. Write the data

1. Replace the contents of `BRIEF` entirely, with `mode: "live"`, `asOf` set to the date
   of the latest price, `retrievedAt` set to now in ISO 8601 with offset, `priceType`, and
   `companies` keyed by symbol.
2. Add one entry at the **top** of `CHANGELOG`:
   - `date`: today, ISO format
   - `summary`: one line per ticker in the form
     `NVDA 182.10 to 188.40 (+3.5%) on strong data center guidance`, where the two prices
     are the first and last price in the new data and the reason is the single most
     relevant news item, or "no major news" if there was none. Then any lines about
     added or removed calendar items, and any lines about missing data.
   - `note`: anything the owner should verify by hand (for example, "JPM earnings date is
     expected, not confirmed").
3. Change nothing else. Not `WATCHLIST`, not the CSS, not the rendering code. If you think
   the page needs a code change, say so in your final message and stop.

## Step 5. Check and report

1. Run the `qa-reviewer` agent on `index.html`. If you cannot launch an agent, say so;
   the main session will run it.
2. Finish with a short report: what you refreshed, the lines you added to the changelog,
   every source URL you used, and what the owner should verify first (any price from a
   secondary source, any "expected" date, any ticker with missing data).

## What you never do

- Invent or estimate a price, a date, a headline, or a URL.
- Use a price from memory. Every number comes from a page you opened during this refresh.
- Mix a close and an intraday price in the same `closes` array.
- Edit anything outside `BRIEF` and `CHANGELOG`.
- Commit or push. The owner does that after reading the diff.
