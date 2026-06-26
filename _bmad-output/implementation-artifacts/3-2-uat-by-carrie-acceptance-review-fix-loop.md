# Story 3.2: UAT by Carrie (acceptance review + fix loop)

Status: done

## Story

As Carrie,
I want to review the complete handbook on the `handbook` branch and have issues fixed,
so that I sign off before go-live.

## Acceptance Criteria

1. **Given** the `handbook` branch, **When** I run `hugo server` locally, **Then** I can review the full handbook (all sections/topics, Start Here, search, stubs, Go Deeper, brand, accessibility) — Amplify previews being disabled.
2. **Given** issues I log during UAT, **When** they are triaged, **Then** they are addressed on the `handbook` branch and re-verified, looping until I sign off.
3. **Given** my sign-off, **Then** the branch is acceptance-complete and ready for the go-live PR (Story 3.3).

## How this is run

Carrie reviews live on the local `hugo server` (http://localhost:1313/handbook/). She logs issues conversationally; each is fixed on the `handbook` branch, rebuilt, and re-verified (build green + visual/Playwright check) before moving on. This file is the running UAT issue log. Sign-off flips this story to `review`/done and unblocks Story 3.3.

## UAT issue log

| # | Date | Issue | Resolution | Files | Verified |
|---|------|-------|------------|-------|----------|
| U1 | 2026-06-25 | Handbook left menu (section tree) disappears at small/mobile widths — the hamburger drawer showed only Search · Posts · Handbook · About · theme, with no way to navigate the handbook's sections/topics on a phone. | Root cause: Hextra's mobile drawer renders a nav entry's nested tree only when the entry is **page-backed**; the "Handbook" `[[menu.main]]` used a literal `url`, so it rendered as a flat leaf. Changed it to `pageRef = "/handbook"` (safe now that `/handbook` exists). Mobile drawer now shows the full 12-section tree, auto-expanding the active section and highlighting the current topic; desktop sidebar + navbar unchanged. | `carriekroutil-site/hugo.toml` | Build green; verified at 600px (drawer shows tree, Posts/About still present) + 1280px (desktop intact). |
| U2 | 2026-06-25 | The 12 top-level section index pages felt light/unintuitive — Hextra's default renders only the section's intro paragraph, leaving the main column empty; topics were reachable only via the sidebar (no in-content way to see/click what's in the section). | Enhanced the section-index branch of `layouts/docs/list.html`: after the intro it now auto-renders an **"In this section"** card grid (one card per topic: title + description + 🚧 badge on stubs). Auto-derived from `.Pages.ByWeight` — **zero upkeep** (new topics appear automatically; badge from the `stub` flag). New `.ck-sec-cards`/`.ck-tcard` CSS uses existing `--ck-*` tokens only (theme-aware, reduced-motion); reuses `.ck-stub-badge`. Applies to all 12 sections uniformly. *(A "New here? Start with {first topic} →" lead-in was prototyped then removed at Carrie's request — the cards alone are enough.)* Spacing: scoped `.content + .ck-eyebrow { margin-top: 2rem }` so the "In this section" gap matches the Start Here eyebrow gap (measured 32px both); Start Here unaffected. | `carriekroutil-site/layouts/docs/list.html`, `assets/css/custom.css` | Build green; verified The Transition (5 stub cards w/ badges) + People & Leadership (5 content cards, no badges) in dark + light; gap = 32px (matches Start Here); grid reflows via `auto-fill minmax(240px)`. |
| U3 | 2026-06-25 | (idea) Add a per-section visual (image/banner) to the 12 section pages for look-and-feel. | **Declined by Carrie** — the U2 cards already resolved the light/unintuitive feel; no images. | — | closed (won't do) |
| U4 | 2026-06-25 | Every handbook page showed a page scrollbar even when nearly empty. (Carrie's hypothesis: the hidden light/dark toggle still in the DOM pushing the footer down.) | **Tested the hypothesis and ruled it out** — the duplicate toggle is `display:none` on desktop (Story 1.3), so it has no layout box; measured overflow = exactly the footer height (123px). Real cause: Hextra forces the docs content row (`<article>` **and** the sticky sidebar) to ~`calc(100vh − navbar)` regardless of content, then stacks the footer below → every docs page overflows by the footer height. Fix: added `--ck-footer-h: 123px`; the docs `<article>` now uses `.ck-docs-article { min-height: calc(100vh − navbar − footer) }` (replaces the forced Tailwind min-h on all 3 docs articles), and the desktop sidebar scroll area is capped to the same height (toggle hidden on desktop, so `--menu-height` reservation isn't needed). Short pages now fit with the footer pinned at the bottom and no scrollbar; pages only scroll when content genuinely exceeds the viewport. | `carriekroutil-site/assets/css/custom.css`, `layouts/docs/list.html`, `layouts/docs/single.html` | Build green. Measured at 1280×1100: section page overflow 123px→0, footer pinned at viewport bottom. Parent site unaffected (`.ck-docs-article` absent; sidebar `display:none` at desktop). Long-content pages still scroll naturally (correct). |

## Sign-off

- [x] Carrie's UAT sign-off (2026-06-25) — approved; proceeded to Story 3.3 go-live. U1 (mobile sidebar), U2 (section cards), U4 (page scrollbar) shipped; U3 (images) declined.

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | UAT started. U1: fixed mobile handbook sidebar (literal `url` → `pageRef` on the Handbook nav entry so Hextra renders the section tree in the mobile drawer). Committed/pushed status tracked separately. |
| 2026-06-25 | U2: section index pages now auto-render a "Start with…" lead-in + topic-card grid (zero upkeep). U3 (per-section image) logged as open idea pending a prototype. |
| 2026-06-25 | U2 follow-ups: removed the "New here? Start with…" lead-in (cards only); matched the "In this section" gap to the Start Here eyebrow (2rem). U3 declined (no images). |
| 2026-06-25 | U4: fixed the always-present handbook page scrollbar — docs article + desktop sidebar now leave room for the footer (`--ck-footer-h`). Tested & ruled out the hidden-toggle hypothesis; real cause was Hextra's forced full-height content row + stacked footer. |
| 2026-06-25 | Committed U1–U4 to `handbook` as `bd094b6` and pushed; PR #21 (still DRAFT) updated — now 8 commits (Epics 1–2, Story 3.1, Story 3.2 UAT). UAT continues. |
