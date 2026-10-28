---
name: dashboard-page
description: How index.html in this project is built and what "done" means. Use for any change to the look, layout, or behavior of the page. Not for refreshing data (that is the refresh-brief skill).
---

# Dashboard page

## Purpose of the page

A reader opens it on a phone on Monday morning and in under two minutes knows: which of
the five names moved, why, and what is coming. That is the whole job. Anything that does not
serve it is removed.

## Structure (top to bottom)

1. **Masthead**: title, the watchlist owner's name, "As of" date, price type (close or
   intraday with the time), and a sample-data banner when `BRIEF.mode === "sample"`.
2. **The tape**: one row per ticker. Ticker and company name on the left, a sparkline of the
   closes in the middle (plus the intraday point if present), last price and weekly change
   on the right. Up is green, down is red, flat or missing is grey. This is the signature
   element of the page; keep it clean.
3. **Per-company sections**: for each ticker, "What happened" (up to three dated news
   items, each with a source link and a one-line "why it matters") and "Coming up" (dated
   events inside the next two weeks with a source link). Empty lists render a short grey
   sentence, never a blank space.
4. **Calendar**: every upcoming event across all tickers, merged and sorted by date.
5. **What changed**: the `CHANGELOG`, newest first. Each entry shows its date and the
   one-line summaries from that refresh.
6. **Footer**: "Unofficial. Not investment advice. Numbers were collected by an AI agent
   and verified by [owner]. Sources are linked." and the repository link if set.

## Data contract

Rendering code reads only `WATCHLIST`, `BRIEF`, and `CHANGELOG`. Shapes:

```js
const WATCHLIST = {
  owner: "Your Name",
  club: "SIBC",            // or "AIM", or "" if none
  repo: "",                // optional https URL of the GitHub repo
  tickers: [
    { symbol: "NVDA", name: "NVIDIA", exchange: "NASDAQ", why: "One line on why it is on the list." },
  ]
};

const BRIEF = {
  mode: "sample",          // "sample" until the first real refresh, then "live"
  asOf: "2026-10-17",      // date of the latest price in the data (YYYY-MM-DD)
  retrievedAt: "2026-10-17T16:30:00-04:00",
  priceType: "close",      // "close" or "intraday"
  companies: {
    NVDA: {
      closes: [ { date: "2026-10-13", close: 100.00 }, /* five trading days, oldest first */ ],
      latest: null,        // { date, time, price } when priceType is "intraday", else null
      priceSource: "https://...",
      news: [ { date: "2026-10-15", headline: "...", source: "Reuters", url: "https://...", why: "..." } ],
      upcoming: [ { date: "2026-10-28", event: "Q3 earnings after close", source: "Company IR", url: "https://..." } ]
    }
  }
};

const CHANGELOG = [
  { date: "2026-10-17", summary: [ "NVDA 100.00 to 104.20 (+4.2%) on ...", "Added JPM Q3 earnings date" ], note: "" }
];
```

Rules the code enforces:

- Weekly change is `(last - first) / first` over the `closes` array, using `latest.price`
  as `last` when present. Never typed by hand.
- Missing data is allowed everywhere: `closes` may be shorter than five, `latest` may be
  `null`, `news` and `upcoming` may be empty, a ticker may be in `WATCHLIST` with no entry
  in `BRIEF.companies`. The page says what is missing in plain words.
- Dates are formatted by the page from ISO strings. Do not type formatted dates in data.

## Style

- One `<style>` block, one `<script>` block, nothing external (Google Fonts allowed but
  currently unused).
- Colors are CSS variables on `:root`. Navy and gold base, green and red reserved for
  direction only. Never use green or red for anything except price direction.
- Numbers use tabular figures (`font-variant-numeric: tabular-nums`) so columns line up.
- Sparklines are inline SVG drawn from the data, 120 by 36 viewBox, stroke only, no fill,
  no axes, no gridlines. A dot marks the last point.
- Readable at 380px wide with no horizontal scroll. Tap targets at least 44px tall.
- Print: the tape and the calendar on page one, per-company sections after; hide the
  changelog and any controls; black text on white.
- No italics anywhere on the page. No em dashes or en dashes in text; use commas, colons,
  periods, or parentheses. Number ranges say "to".

## Definition of done

- Opens from a double-click on the file, with no console errors.
- Every number on the page can be traced to `BRIEF`; every link in a news or calendar item
  comes from `BRIEF`.
- Renders correctly with `BRIEF.companies` set to `{}` (all missing).
- `qa-reviewer` reports "Ready to publish".
