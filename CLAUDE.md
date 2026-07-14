# CLAUDE.md

Guidance for AI assistants (and humans) working in this repository.

## What this is

A **static Korean mobile wedding invitation** (모바일 청첩장) for
김서우 (Seowoo Kim) ♥ 양은진 (Eunjin Yang), wedding on **2026-10-10 (Sat) 12:00 KST**
at **앰배서더 서울 풀만 호텔 19층 남산룸** (Ambassador Seoul Pullman Hotel, 19F Namsan Room).

There is **no build step, no framework, no package manager, and no backend**. Every page
is a self-contained HTML file with inline `<style>` and inline vanilla JavaScript. It is
served as-is from **GitHub Pages** at `https://bartbit00.github.io/wedding-invite-1010/`.

The entire UI is in **Korean**, and layouts are designed **mobile-first** (fixed max width
of `430px`, centered on desktop). Preserve the Korean copy and the mobile framing when editing.

## Layout

```
/
├── index.html          # Root invitation — the CONDENSED (축소본) single-page design
├── cover.jpg           # Cover photo used by index.html + its OG preview image
└── clone/              # The FULL-featured template clone (self-contained sub-site)
    ├── index.html      # Full invitation: gallery, RSVP, kakao map, accounts, crypto gift
    ├── calendar.html   # "Add to calendar" landing page (Google Calendar + .ics)
    ├── wedding.ics     # iCalendar file for Apple / other calendars
    └── images/         # main1/main2.jpg, gallery g01–g28.jpg, crypto-qr.jpg
```

### Two independent variants — know which one you're editing

This repo holds **two separate invitation designs** that do **not** share code:

- **`/index.html`** — a lighter, warm-cream "condensed" version (~250 lines). Sections:
  cover, greeting, groom & bride, calendar, location, information, RSVP, guestbook, share.
  Most interactive features here are **placeholders** that only show a toast
  ("배포 단계에서 연결됩니다" / "connected at deploy time"). It computes a live D-day countdown.

- **`/clone/index.html`** — the full-featured template (~1080 lines). This is the more
  actively developed variant (see git history). It has a real gallery + lightbox, an RSVP
  modal wired to a Google Apps Script endpoint, an embedded Kakao Map with a fallback card,
  navigation-app deep links, a collapsible accounts (마음 전하실 곳) section with a crypto/USDT
  gift QR, background-music toggle, Kakao share card, and a table-of-contents overlay.

When asked to change "the invitation", **confirm which variant** unless context makes it
obvious. A change to one file does not propagate to the other.

## Conventions

- **Single-file pages.** CSS lives in one `<style>` block in `<head>`; JS lives in one
  `<script>` at the end of `<body>`. Do not introduce external CSS/JS files, bundlers, or
  npm dependencies — keep each page self-contained so it works when opened directly.
- **Vanilla JS only, ES5-flavored.** The `clone` script is wrapped in an IIFE with
  `"use strict"`, uses `var`, `function` expressions, and feature-detects APIs
  (`IntersectionObserver`, `navigator.clipboard`, `navigator.share`) with graceful fallbacks.
  Match this style; do not add a framework or transpiler.
- **CSS custom properties for theming.** Colors, fonts, and the `--maxw:430px` max width are
  defined in `:root`. Reuse these variables rather than hard-coding values.
- **Fonts and SDKs come from CDNs only.** Google Fonts, and (in `clone`) the Kakao JS SDK and
  Kakao Maps SDK, are loaded via CDN `<link>`/`<script>`. No font files are vendored.
- **Comments and commit messages are in Korean.** The codebase is authored in Korean; match
  the existing language and tone when adding comments. Commit messages in history are short,
  imperative Korean summaries — follow that convention.
- **Graceful degradation everywhere.** Clipboard copy falls back to a hidden `<textarea>` +
  `execCommand`; the Kakao map falls back to a static card; share falls back to Web Share API
  then link-copy. Preserve these fallbacks when touching related code.

## Configuration hotspots (in `clone/index.html`)

Deployment-time settings are centralized in the `CONFIG` object inside the `clone` script
(around line 751). This is the intended place to wire real integrations:

- `RSVP_ENDPOINT` — Google Apps Script web-app URL; RSVP submissions POST here (`no-cors`,
  optimistic success) to land in a Google Sheet. Empty → demo mode (console + `alert`).
- `KAKAO_JS_KEY` — Kakao JavaScript key for the share card / SDK (domain-restricted).
- `SITE_URL` — canonical deployed URL, used to build share links and image URLs.
- `EVENT` — title / dates / location / description used to build the Google Calendar URL.

Other things that are still placeholders and expected to be filled before the real event:

- **Bank account numbers** are literally `[은행 000-0000-0000]` in `clone/index.html`.
- **OG image URLs** point at the GitHub Pages absolute path — keep them absolute so
  KakaoTalk/social previews resolve.

The wedding date/time is duplicated in several spots (root `index.html` D-day script,
`clone` `CONFIG.EVENT` / `gcalDates`, `calendar.html`'s Google Calendar link, and
`wedding.ics`). **If the date, venue, or names change, update every one of them** and keep
UTC/KST conversions consistent (12:00–14:00 KST = `03:00–05:00Z`).

## Running / previewing

No build. Open a file directly, or serve the folder for correct relative-path and SDK behavior:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/            (root condensed version)
#            http://localhost:8000/clone/       (full version)
```

Note: the Kakao Map and Kakao share card are **domain-restricted** and generally only work
on the registered production domain, not on `localhost` — expect the map fallback card locally.

## Deployment

The site is published via **GitHub Pages** from this repo (`bartbit00/wedding-invite-1010`).
Absolute URLs in OG tags and in `CONFIG.SITE_URL` assume the
`https://bartbit00.github.io/wedding-invite-1010/` base. There is no CI/build pipeline —
pushing to the published branch updates the live site directly, so verify changes render
before pushing.

## Git workflow

- Feature branches follow the pattern `claude/<topic>` (this work is on
  `claude/claude-md-docs-l6yh2p`); the default branch is `main`.
- Keep commit messages short and in Korean, consistent with the existing history.
- Since a push goes straight to a live static site, sanity-check the affected page(s) in a
  browser before pushing.
