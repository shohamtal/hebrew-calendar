# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Local development

```bash
python3 -m http.server 5199
# then open http://localhost:5199
```

Port **5199 is the only one authorized** in the Google Cloud OAuth client's JavaScript origins. Using any other port causes `redirect_uri_mismatch` on sign-in. Don't switch ports without updating the client config in Google Cloud Console first.

There is no build step, no bundler, no test suite, no linter. The app is a single HTML file served directly.

Deployment is GitHub Pages at https://shohamtal.github.io/hebrew-calendar/ — pushing to `main` publishes.

## Architecture

The entire app lives in `index.html` (~712 lines). `privacy.html` and `terms.html` are linked from the footer and served as static siblings.

Three external dependencies, all loaded from CDN at runtime — never bundled:

- `@hebcal/core` via `https://esm.sh/@hebcal/core@5` — Hebrew ↔ Gregorian date conversion (`HDate`, `HebrewCalendar`, `Event`). Imported via `<script type="module">`.
- Google Identity Services (`https://accounts.google.com/gsi/client`) — OAuth 2.0 token client, loaded dynamically from JS.
- Google Calendar API v3 — called via plain `fetch` with the bearer token, no client library.

OAuth scope is `calendar.events.owned` (the narrowest scope that still lets the app create events on the user's primary calendar). The OAuth client ID is embedded in the source at `index.html:336` — this is intentional, not a secret; Google enforces the `Authorized JavaScript origins` allowlist. If you change origins, update the Google Cloud Console entry for that client ID.

### The two features

Both tabs follow the same pipeline: **build preview array → render table → user confirms → POST each event to Calendar API with a progress bar and per-row log**.

- **Omer Counting (`previewOmerEvents` / `confirmOmerEvents`)**: For each selected Gregorian year, computes the Hebrew year as `year + 3760`, anchors day 1 at `15 Nisan` (not 16 — see comment at `index.html:531`: the default reminder time of 20:00 is after sunset, so calling the evening of 15 Nisan "day 1" is halachically correct), then generates 49 daily events. Hebrew text for the count is built by hand in `hebrewDayCount`/`hebrewWeekCount`/`hebrewDayRemainder` — do not replace with a library call without checking the grammar (masculine, with special forms for 1, 2, and teens).
- **Hebrew Date Reminder (`previewHebrewDateEvents` / `confirmHebrewDateEvents`)**: Converts a (day, month) pair to Gregorian for each selected civil year. Hebrew year offset depends on the month: Nisan–Elul use `+3760`, Tishrei–Adar II use `+3761` (the Hebrew year rolls over at Tishrei). Invalid dates (e.g. 30 Cheshvan in a short year) are caught per-row and rendered as an error row rather than aborting the batch.

### Rate limiting

Both confirm flows send events sequentially. The Omer flow sleeps 500ms every 10 events (`index.html:603`) to stay under Calendar API quotas; the Hebrew Date flow doesn't (only up to 10 events per run). Preserve this if editing.

### State

Three module-level variables hold all state: `accessToken`, `pendingOmerEvents`, `pendingHebrewEvents`. No framework, no store — handlers are attached to `window.*` so inline `onclick=` in the HTML can reach them.

## Language and RTL

All UI text is Hebrew; the document is `dir="rtl"`. When editing strings or adding UI, keep RTL layout in mind (the tabs intentionally swap `border-radius` corners at `index.html:74-75`).
