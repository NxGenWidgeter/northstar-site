# Northstar — site

The marketing page for **Northstar**: a local desktop panel that shows the weather, the
next event, device health, headlines and the day's events in one narrow window. Everything
is free — no tier, no account, no tip jar — and the only ask is a bug report or an idea.

Plain HTML/CSS/JS — one file plus a folder of screenshots. No build step, no fonts to
fetch, no dependencies at all. Open `index.html` in a browser to view it, or drag the
folder into any static host: it is deployed at
https://nxgenwidgeter.github.io/northstar-site/ from a public repo of just this folder.

```
index.html               the whole site (CSS + JS embedded)
assets/                  real captures of the panel (PNG, 2x)
  panel_dark.png         full panel, dark theme      800×1598
  panel_light.png        full panel, light theme     800×1598
  strip_forecast.png     the glance strip, opened    800×370
  card_health.png        this-device card            800×386
  card_news_world.png    headlines, world scope      800×414
  card_events_cal.png    events + subscription open  800×798
  card_bookit.png        Book it, expanded           800×338
README.md                this file
```

Upload the site folder to any static host — Netlify, Cloudflare Pages, GitHub Pages,
shared hosting — and it works. The screenshots are the only assets.

The captures are real renders of the panel, but never of a personal one: the demo
instance is seeded with invented events and a neutral hostname so no shot carries
anybody's calendar, host or name.

    python3 tools/site-demo/seed.py /tmp/ns-demo/data
    cp -a linux_version/. /tmp/ns-demo/ && cd /tmp/ns-demo
    unshare -ru sh -c 'hostname demo-station; NORTHSTAR_PORT=8890 exec python3 server.py'

Then Chromium at 400 px wide, device scale 2, transparent canvas, per shot setting
theme/fold/scope through the UI (or localStorage) and clipping the card's own rect.
The full recipe lives in the header of `tools/site-demo/seed.py`.

---

## What is on the page

| Section | id | Content |
|---|---|---|
| Nav | — | Polaris mark, What's inside / The panel / Price / Questions, theme toggle, "Get the early build" |
| Hero | — | Headline, lead, CTAs, the real panel capture (swaps between dark and light with the theme) |
| 01 Today | — | `strip_forecast.png` + the glance strip, Open-Meteo, PM2.5, the cached forecast |
| 02 This device | `#features` | Copy + `card_health.png` + mono spec rows (`/proc`, compositor, root) |
| 03 Headlines | — | `card_news_world.png` + the LOCAL/WORLD story, the merge rule and the offline cache |
| 04 Events | — | `card_events_cal.png` + .ics subscriptions, recurrence, export, reminders |
| Book it | — | `card_bookit.png`, deep links, the collab preview, no accounts |
| 05 Theme | `#looks` | Dark and light panels side by side |
| Privacy | — | "Nothing leaves this machine. Except a few RSS fetches, one weather lookup — and a usage summary, only if you tick the box." |
| How it works | `#how` | Download, Connect, Fold, Pass it on (four mono-numbered steps) |
| Price | `#pricing` | One plan: free, no account, no subscription |
| Help | `#support` | A mailto for bugs and ideas, then the board of people who helped |
| Questions | `#faq` | Seven `<details>`, first open by default |
| Early build | `#contact` | Name, email, platform, "what would you want it to show", consent |

## The brand

- **Name**: `Northstar` — title, meta tags, nav, footer and page copy.
- **Mark**: *Polaris* — a ringed star, a link, and two smaller dots, drawn as inline
  SVG in the nav and footer. It replaced the old four-point sparkle, which sat too close
  to Gemini's logo.

  ```html
  <g fill="none" stroke="currentColor">
    <path d="M4.6 19.4 L19.2 5.6" stroke-width="1.25" opacity=".5"/>
    <circle cx="4.6" cy="19.4" r="1.7" fill="currentColor" stroke="none" opacity=".72"/>
    <circle cx="12.4" cy="12.4" r="2" fill="currentColor" stroke="none" opacity=".92"/>
    <circle cx="19.2" cy="5.6" r="3" stroke-width="1.5"/>
    <circle cx="19.2" cy="5.6" r="1.1" fill="currentColor" stroke="none"/>
  </g>
  ```

  Keep the ring inside the 24-unit box: `cx 19.2 + r 3 + half the 1.5 stroke = 22.95`.
- **Small variant**: below ~16 px the three dots turn to mush, so the app's header and
  the favicon use the ringed star on its own.
- **Favicon**: inline SVG data URI in the `<head>`, blue mark on a near-black tile.
- **Accent**: blue `#7FB2FF` on `#06070A`; gold `#E7B25A` survives only as the
  publish-time warning under the coffee link.
  Colours are lifted from the panel itself (`ui/style.css`), so site and app match.

### Colours and type

Everything lives in the `:root` block at the top of `<style>`; the light theme is the
same tokens under `:root[data-theme="light"]`:

```css
--bg: #06070A;        /* page */
--bg-2: #0A0C11;      /* alternating bands, footer */
--surface: #0E1116;   /* cards, inputs */
--shot: #0B0D12;      /* frame behind every screenshot */
--line: #1E232D;      /* hairlines */
--ink: #F2F5FA;       /* headings */
--dim: #98A3B4;       /* body copy */
--dimmer: #6B7484;    /* mono labels, captions */
--accent: #7FB2FF;    /* the only interactive colour */
--gold: #E7B25A;      /* placeholder warning only */
```

Type is **Cantarell** (the app's own UI face) with **DejaVu Sans Mono** for kickers,
labels, captions and spec rows. Both are local — there is no Google Fonts `<link>`, so
the page renders identically offline and nothing is fetched from a third party.

## The theme toggle

The half-disc button in the nav flips the whole site between the dark and light token
sets, stores the choice in `localStorage` under `northstar.site.theme`, and swaps the
hero capture (`shot-dark` / `shot-light`) to match. That mirrors what the app does, so
the page demonstrates the feature instead of describing it.

## Placeholder copy — replace before publishing

- [ ] **Prices** — none to check: the page says everything is free. If a tier ever comes
      back, the three-plan markup is in git history (commit before 2026-11-14).
- [x] **Email** — set to `nxautomation003@gmail.com` (contact row, form note, footer,
      Help CTA, form hand-off). The form has no backend: it composes a `mailto:` with the
      answers and opens the visitor's mail app, so the address has to stay valid or the
      whole contact path dies with it.
- [x] **Reply time** — was "Replies within two days". Now "Replies are slow but real"
      and "From a person, not a bot": no SLA to break, and both lines stay true.
- [ ] **Platform claims** — Linux is the daily driver; Windows ships as a per-user
      installer but has not been run on real Windows hardware yet, so the page calls it a
      preview and macOS has no build at all. Keep that honest until each is tested.
- [ ] **Tip jar** — dormant on purpose, and it must stay dormant until the account exists.
      The block (`#tipline`) ships `hidden` with `href="#"`, and the Help section carries
      two sets of copy (`data-copy="dormant"` / `"live"`) so the headline never says "No
      tip jar" above a tip button. Switch on with `tools/set-support-links.py <handle>`,
      back off with `--reset`, and read `docs/support.md` first — the account has to be
      opened by an adult, and Ko-fi's Standard mode has to stay off or it takes 5%.
- [ ] **The board** — a `<ul>` in `#support`, one `<li>` per person who helped (tester,
      bug report, idea, translation), newest first, with a template in an HTML comment.
      Only add a name the person agreed to.
- [ ] **`og:image`** — still `https://yourdomain.com/share.jpg`.
- [ ] **Screenshots** — they are genuine captures, so they show the real device numbers,
      real Malaysian headlines and the real "312 events" holiday feed of the machine they
      were taken on. Re-shoot them on a clean account before publishing.
- [ ] **Privacy copy** — the page says usage counts are off by default and lists what is
      in the summary. `docs/privacy.md` in the repo is the authority; a change to either
      means changing both, or the site becomes a promise the app breaks.

## The early build form

Front-end only: it validates, shows a demo message and sends nothing. To make it real:

1. Pick a form service (Formspree, Netlify Forms, Basin) or your own endpoint.
2. Set the form's `action` to that URL and `method="POST"`.
3. Delete the `/* ---------- Form (demo only, no backend) ---------- */` script block
   at the bottom of the file, so the browser submits normally.

---

## Notes on the build

- **Layout**: 1180px wrap, breakpoints at 1000px (columns collapse), 860px (mobile menu,
  `calc(100dvh)` panel) and 620px (steps, plans and the theme pair go single column).
- **Screenshots sit in dark frames** (`.shot`), with a mono caption strip underneath.
  That is deliberate on the light theme too: the panel is a dark app, and a dark inset
  reads as a device rather than a hole in the page.
- **Hairlines, not cards everywhere**: bands are separated by 1px rules; only the price
  plans and the form are boxed, so the page does not read as a grid of tiles.
- **Accessibility**: skip link, 2px focus rings, labelled fields, `aria-expanded` on the
  menu button, alt text on every capture, native `<details>` for the FAQ, 38–46px hit
  targets.
- **Motion**: sections fade up 14px on scroll via `IntersectionObserver`; the whole
  effect is disabled under `prefers-reduced-motion`. The `.rv` class only starts hidden
  when JS is present, so the page is fully readable without scripts.
- **Form validation**: client-side only (required fields + email pattern), with an inline
  status message that is not colour-only.
- **Verified at** 1440, 1024 and 390 px: no horizontal overflow, no console errors, every
  image loads, the light theme and the mobile menu both exercised.
