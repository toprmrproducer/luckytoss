# LuckyToss

Single-purpose, standalone AstroJS tool. Roll animated dice (1-6, real pip
faces) or flip an animated 3D coin, with streak tracking, running tallies,
and a short session history. 100% client-side — no backend, no accounts,
nothing ever leaves the browser.

This is **deliberately single-purpose**: one tool, one domain, one
keyword-matched intent. It is intentionally NOT a page bolted onto a
multi-tool site. The SEO strategy for this whole tool family is one
focused domain per tool, so it can rank tightly for "dice roller" /
"coin flip" searches instead of competing for attention inside a generic
utility dump. Do not add other tools, a shared nav, or cross-links to
sibling tools (anyconvert, pixelforge, reelshift, stillmotion,
scriptflow, etc.) into this project.

## Stack

- **Astro** ^7 — static output (`output: 'static'`, the default), zero
  server runtime.
- **Tailwind v4** via `@tailwindcss/vite` (no `tailwind.config.js` — theme
  lives in `src/styles/global.css` under `@theme`).
- **canvas-confetti** — celebration burst on hot streaks / max rolls.
- **@astrojs/sitemap** — sitemap-index.xml generated at build time.
- TypeScript (`astro/tsconfigs/strict`).

No React/Vue/server framework. Every interaction (tabs, coin flip, dice
roll, tallies, history) is a single vanilla `<script>` block in
`src/pages/index.astro`, adapted from the working, already-tested logic
in the `anyconvert` sibling project's `dice-coin-flip.astro`
(same random-based fairness, same animation timings, same confetti
triggers), then re-skinned into this project's own design system.

## Design tokens (mandatory — do not deviate)

Defined in `src/styles/global.css` under `@theme`:

| Token | Value | Use |
|---|---|---|
| `--color-cream` | `#FAF6EC` | page background |
| `--color-surface` | `#FFFFFF` | cards/surfaces |
| `--color-border-warm` | `#E8DFC8` | all borders (never gray) |
| `--color-heading` | `#2B2013` | heading text (warm espresso, not pure black) |
| `--color-body` | `#5C4F3D` | body text |
| `--color-gold` | `#C9982E` | accent / CTA / links / active states |
| `--color-gold-dark` | `#B8860B` | hover state for gold |
| `--font-display` | "Fraunces" | all headings |
| `--font-sans` | "Inter" | body, labels, buttons, UI |

Rounded-xl corners, soft shadows only (never harsh), generous
whitespace, no gradients, no dark mode. This is the same family look as
the other split-out single-tool sites (reelshift, stillmotion,
scriptflow, anyconvert) — keep it identical, don't reinterpret it per
project.

## Security

Any text touching `innerHTML` is passed through the local `escapeHtml()`
helper in `index.astro` before insertion, even where the underlying value
is internally generated (defensive-by-default, matching the standing
requirement across this tool family). Prefer `textContent` /
`createElement` + `className` assignment (as used throughout) over raw
HTML string interpolation wherever practical.

## Required pages (AdSense eligibility)

`about.astro`, `privacy.astro`, `faq.astro`, `404.astro` — all linked
from the header/footer of every page via `Layout.astro`. Do not remove
these or make them unreachable from the homepage.

## Ports

Dev server runs on port **4342** (`npm run dev`, `npm run preview`).
Sibling projects already claim: anyconvert (default 4321), reelshift
(4333), scriptflow (4332), stillmotion (4334), pixelforge (default).
Concurrent sibling builds during this project's creation were also
observed claiming 4340 (vitalcalc) and 4341 (hueforge), so 4342 was
picked as unclaimed in the 4340-4360 range assigned to this build.

## iCloud sync hygiene

`node_modules` is a symlink to `node_modules.nosync` (the real
directory), so iCloud does not attempt to sync hundreds of thousands of
package files. `tsconfig.json`'s `exclude` includes **both**
`"node_modules"` and `"node_modules.nosync"` — omitting the second
causes `astro check` / the Astro language server to crash trying to
type-check into the real (non-symlinked) directory.

On a fresh checkout on the other Mac: `npm install` regenerates
`node_modules.nosync` at this same path; do not commit it.

## Deploy process

1. `source ~/.claude/credentials/netlify.env` → exports `NETLIFY_API_KEY`;
   re-export as `NETLIFY_AUTH_TOKEN` for the Netlify CLI.
2. `npm run build` (must succeed with zero errors before deploying).
3. First deploy only: `netlify sites:create --name luckytoss
   --disable-linking`, then write the returned site ID into
   `.netlify/state.json`.
4. `netlify deploy --prod --dir=dist`.
5. Verify the **production** URL directly (not localhost) before calling
   it done.

## What NOT to do here

- Don't add a multi-tool nav or links to other RapidXAI tools.
- Don't introduce a build framework beyond Astro + Tailwind v4 + vanilla
  TS/JS.
- Don't change the design tokens to match a different aesthetic per
  project — the cream/gold family look is intentional and shared.
- Don't skip `node_modules.nosync` renaming or the dual tsconfig
  exclude — both are known gotchas that break things silently later.
