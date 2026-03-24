# Marquee — Agent Guide

## What This Is

A single-page web app that fetches movie showtimes from the Gracenote (TMS) API and displays them grouped by movie or by theater. No backend, no build step — everything lives in `index.html`.

## Key Facts

- **Entry point**: `index.html` (embedded CSS + JS, no build tool — edit directly)
- **All application logic**: `window.Marquee` namespace object in the `<script>` block
- **Dependencies**: `assets/` directory only (dayjs, js-cookie, sjcl, normalize.css) — no CDN, no npm
- **Deployment**: `bundle exec s3_website_revived push` (AWS credentials in `.env`)
- **`files/`**: reference mockups and a sample API response — never deployed

## Architecture

```
Form submit → fetchMovieTimes() → processShowtimes() → renderShowtimes()
```

Key data structures on `Marquee`:
- `apiShowtimeCache` — keyed by `zip--radius--date`; prevents redundant API calls
- `dataForMovie` / `dataForTheater` — metadata indexed by `rootId` / `theatreId`
- `showtimesByMovie` / `showtimesByTheater` — hierarchical groupings for the two view modes
- `showtimeBlocks` — pre-rendered HTML fragments keyed by `movieId--theaterId`

## API

The Gracenote TMS API key in the HTML is AES-encrypted via SJCL and decrypted at runtime with the password `casablanca`. See `files/api.txt` for a representative response.

## Common Tasks

- **UI changes**: `<style>` block in `index.html`; breakpoints at `600px` and `900px`
- **Add/change metadata**: `createMovie()` populates `dataForMovie`; `createShowtimeHTML()` renders it
- **Add a filter**: form HTML → `serializeForm()` → `fetchMovieTimes()`
- **Add a view mode**: follow `showtimesByMovie`/`showtimesByTheater`; update `renderShowtimes()` and `changeGrouping()`

## Constraints

- Tabs, not spaces
- Do not split `index.html` into separate files — the monolithic structure is intentional
- Do not commit `.env`
