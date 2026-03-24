# Marquee — Agent Guide

## What This Is

A single-page web app that fetches movie showtimes from the Gracenote (TMS) API and displays them grouped by movie or by theater. No backend, no build step — everything lives in `index.html`.

## Key Facts

- **Entry point**: `index.html` (embedded CSS + JS, no build tool — edit directly)
- **All application logic**: `window.Marquee` namespace object in the `<script>` block
- **Deployment**: `bundle exec s3_website push` (AWS credentials in `.env`)
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

The Gracenote TMS API key is AES-encrypted and stored in `Marquee.apiKeyEncrypted`. It is decrypted at runtime via the Web Crypto API (`crypto.subtle`) using PBKDF2 key derivation, with the password `casablanca` supplied by a hidden form field. See `files/api.txt` for a representative response.

## Common Tasks

- **UI changes**: `<style>` block in `index.html`; one breakpoint at `900px`
- **Add/change metadata**: `createMovie()` populates `dataForMovie`; `createShowtimeHTML()` renders it
- **Add a filter**: form HTML → `serializeForm()` → `fetchMovieTimes()`
- **Add a view mode**: follow `showtimesByMovie`/`showtimesByTheater`; update `renderShowtimes()` and `changeGrouping()`

## Constraints

- Tabs, not spaces
- Do not split `index.html` into separate files — the monolithic structure is intentional
- Do not commit `.env`
