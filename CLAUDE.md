# Ruben Plazier — Website (CLAUDE.md)

Context file for Claude Code. Read this before making changes.

## What this is
A single-page static website (`index.html`, no build step, no framework) for Ruben Plazier,
classical pianist. Plain HTML/CSS/JS. Hosted on Netlify, auto-deploys on push to `main` via
the connected GitHub repo. Domain: rubenplazier.com.

## Workflow — always do this
After making any change:
1. Test that `index.html` still opens correctly (no broken tags, valid JS).
2. `git add -A && git commit -m "short description"` and `git push`.
3. Netlify auto-deploys from the push — no manual deploy step needed.
Never leave changes uncommitted; the user expects "ask Claude Code → live site updates".

## File structure
```
index.html          — everything: HTML + <style> + three <script> blocks
assets/              — images, video, press kit zip (NOT in a build pipeline, referenced
                       with plain relative paths like assets/foo.jpg)
```
The large press-kit zip (`assets/ruben-plazier-press-kit-hq.zip`, ~200MB) is NOT tracked in
git (too big for GitHub). Don't try to commit large binary files — check file size first;
anything over ~10-15MB should probably not go into the repo. Ask the user how to host it
if it needs to change.

## Design system
- Palette (CSS variables at `:root`): `--paper` (bg), `--black` (ink/text), `--red` (#e3392b,
  primary accent), `--blue` (#1d3ea8), `--yellow` (#f2c230), `--muted` (secondary text),
  `--line`/`--rule` (hairlines), `--onphoto` (fixed light color for text over photos —
  never swap this for `--paper`, it must stay legible in both themes).
- Dark mode: `:root[data-theme="dark"]` overrides `--paper`/`--black`/`--muted`/`--line`/`--rule`.
  Toggled via the moon/sun button in nav (`#themeToggle`), with a brief black
  overlay flash (`.lights-overlay`) on switch — this is intentional ("lights out" effect),
  don't remove it.
- Fonts: `Archivo Black` (display/headlines, class `.display`), `Space Grotesk` (body),
  `JetBrains Mono` (labels/eyebrows, class `.mono`).
- Aesthetic reference: De Stijl / Bauhaus, bold and editorial, NOT soft/rounded. Sharp
  corners, hairline rules, generous whitespace, red as the one loud accent color.

## Key interactive features — do not break these
- **i18n (4 languages)**: every user-facing string has `data-i18n="key"` (textContent) or
  `data-i18n-html="key"` (innerHTML, for strings with embedded `<span>` etc). Translations
  live in a `translations` object (`en`/`nl`/`fr`/`de`) near the bottom script block.
  **When adding new visible text, always add the `data-i18n` attribute AND the key to all
  four language blocks** — don't leave new copy untranslated.
- **Agenda auto-hide**: each `.agenda-row` has `data-date="YYYY-MM-DD"`. JS
  (`filterAgendaByDate`) hides past events automatically and shows only the next 2 upcoming
  (`MAX_VISIBLE_AGENDA = 2`), with an expand toggle (`#agendaToggle`) to show the rest.
  Date display (day/month) is generated from `data-date`, not typed manually — when adding
  a concert, only set `data-date` + `.program` + `.venue` text + ticket link.
- **Gallery lightbox**: `.gallery-item` images open in `#lightbox` on click, with prev/next
  and Escape-to-close. If you add gallery photos, they need to be inside a `.gallery-item`
  div with a real `<img>` tag (masonry layout via CSS `columns`, not a rigid grid — images
  keep their natural aspect ratio, never force-crop).
- **Self-playing piano** (`.studio` section): synthesized Web Audio piano that plays itself
  softly on a loop and paints a small abstract canvas above it. Muted by default
  (`#muteBtn`, defaults to `muted = true`). Don't change this default without being asked.
- **Photo frames** (`.ph` class): background-image divs with a curtain-reveal
  (`clip-path`) animation on scroll, plus subtle parallax. Caption/credit text on top of
  photos uses `var(--onphoto)`, never `var(--paper)` (breaks in dark mode).

## Content notes
- Bio (short + long) covers: Delft 1998 → Dordrecht → Wibi Soerjadi spark → Rotterdam
  Conservatory under Stéphane De May → introduced to Jean-Bernard Pommier → Béziers →
  weekly Paris/Rotterdam commute → École Normale de Musique Alfred Cortot diplomas →
  concerto soloist (Rachmaninoff, Mendelssohn, Poulenc, Mozart) → curates classical
  programming for De Doelen Rotterdam (season 26/27) and Muziekgebouw Eindhoven → founded
  Experience Klassiek and its Academy with violinist Karen Su.
- There's a dedicated tribute section to Jean-Bernard Pommier (mentor) with video + photos —
  treat this content as emotionally important to the client, don't rewrite the tone without
  being asked.
- Performances section: Solo / Duo (Karen Su) / Trio (Karen Su & Luc Tooten) / Programming
  (Muziekgebouw Eindhoven, links to mge.nl/en/classical).
- Photo credits matter — keep photographer attribution (Björn Comhaire, Foppe Schut, Bram
  Saeys) wherever it currently appears; these are Wikimedia/press credits with attribution
  requirements, not decorative.

## What NOT to do
- Don't add a build step, framework, or npm dependencies — this stays a plain static file.
- Don't use `localStorage`/`sessionStorage` — not used anywhere currently, keep it that way
  unless explicitly asked (some earlier hosting contexts didn't support it; no reason to add
  the complexity now that it's self-hosted, but keep asking the user first).
- Don't silently change the muted-by-default piano, the dark-mode default, or the max-2
  agenda limit — these were explicit user decisions.
