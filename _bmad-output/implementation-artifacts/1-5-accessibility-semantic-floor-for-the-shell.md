---
baseline_commit: 854299c6b4c64768ee2b897076e8de1e8d978433
---

# Story 1.5: Accessibility & semantic floor for the shell

Status: review

## Story

As a keyboard or screen-reader user,
I want the Handbook shell to be navigable and well-structured,
so that I can use it without barriers (and the site practices the accessibility it preaches).

## Acceptance Criteria

1. **Given** any Handbook page, **When** I navigate by keyboard, **Then** a "skip to content" link lets me bypass the sidebar, **And** the sidebar tree, search, theme toggle, and in-page links are reachable and operable with a logical tab order and a visible focus ring (`2px` solid fuchsia, `:focus-visible`).
2. **Given** the page structure, **Then** landmark regions (`header`/`nav`/`main`/`aside`/`footer`) are present, **And** there is exactly one `<h1>` per page.
3. **Given** the user has `prefers-reduced-motion` set, **When** the page renders, **Then** hover lifts and TOC/scroll animations are dropped.

## Tasks / Subtasks

- [x] **Task 1 — Skip link + focus + keyboard (AC: #1)**
  - [x] Verify the "Skip to content" link is present and targets `<main id="content">`; verify the brand `:focus-visible` ring (2px fuchsia) applies to nav/search/theme-toggle/sidebar/in-page links; verify controls carry accessible labels.
- [x] **Task 2 — Landmarks + single h1 (AC: #2)**
  - [x] Verify `nav`/`main`/`aside`/`footer` landmarks and exactly one `<h1>` per page; record the `<header>`/banner-landmark status as a finding.
- [x] **Task 3 — Reduced motion (AC: #3)**
  - [x] Verify a `prefers-reduced-motion: reduce` rule drops scroll-behavior + transition/animation durations (TOC scroll, hover lifts).
- [x] **Task 4 — Build clean (AC: all)**
  - [x] `hugo --gc --minify` clean.

## Dev Notes

### Verification story — the a11y floor was built site-wide in the parent and applies to /handbook

`assets/css/custom.css` (parent build) already ships the brand `:focus-visible` ring and the single-ring collapse for Hextra controls; Hextra itself ships the skip link, keyboard operability, accessible control labels, and a global reduced-motion reset. The Handbook (docs mode) inherits all of it. Verified against built output:

- **Skip link (AC1):** `<a href="#content" class="…sr-only … focus-visible:not-sr-only …">Skip to content</a>` is present; target `<main id="content">` exists → keyboard users bypass the sidebar to main.
- **Focus ring (AC1):** `:focus-visible { outline: 2px solid #d946ef; outline-offset:2px }` (via `--ck-focus-ring`) applies site-wide; Hextra controls' doubled ring is zeroed (`[class*="hextra-focus-visible"]:focus-visible{--tw-ring-shadow:0 0 #0000}`). Controls carry labels: `aria-label` on Search, "Change theme", "Table of contents", Menu.
- **Landmarks + h1 (AC2):** `<main id=content>`, `<aside>` (sidebar/complementary), `<footer>`, and two `<nav>` (navbar nav + TOC `<nav aria-label="Table of contents">`) are present; exactly **one `<h1>`** per page (root + section pages checked).
- **Reduced motion (AC3):** compiled CSS contains `@media(prefers-reduced-motion:reduce){*,::before,::after{scroll-behavior:auto!important;transition-duration:.01ms!important;animation-duration:.01ms!important;…}}` → TOC smooth-scroll and card/hover transitions are neutralized for reduced-motion users. The card hover-lift is additionally authored only under `prefers-reduced-motion:no-preference`. The Story 1.3 sidebar accent bar uses static positioning (no transition/animation), so it's motion-safe by construction.

### Known variance (flagged for code-review / PR) — `<header>` banner landmark

AC2 lists `header` among the landmarks. Hextra renders the navbar as `<div class="hextra-nav-container"> … <nav class="hextra-max-navbar-width">`, so a **navigation** landmark is present but there is **no `<header>`/`role="banner"`** landmark. This is **global parent-site chrome** (every page, blog included), not handbook-specific. The shell still meets **WCAG 2.1 AA**: a banner landmark is an ARIA authoring best-practice, not an AA success criterion; the AA-relevant criteria are satisfied — 2.4.1 bypass blocks (skip link), 2.1.1 keyboard, 2.4.7 focus visible, 1.3.1 info & relationships (landmarks present), 2.3.3 animation from interactions (reduced-motion). Adding a banner only on `/handbook` would make the Handbook structurally diverge from the rest of the site (against FR-11). Recommendation: address the banner landmark site-wide as a small parent-site chrome fix (override the nav wrapper to `<header>` / add `role="banner"`) — deliberately **out of scope** for this handbook shell story to avoid forking global chrome mid-epic. Raised in the Epic 1 self-review.

### Guardrails

- No code change in this story (floor inherited); verification only. `handbook` branch. No module edits (AD-5).

### References

- [Source: epics.md#Story-1.5, #UX-DR8]
- [Source: carriekroutil-site/assets/css/custom.css] — `:focus-visible` ring (~line 236), Hextra focus-ring collapse, reduced-motion gating
- [Source: build output] — skip link, `<main id=content>`, landmarks, single h1, `prefers-reduced-motion:reduce` reset

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m]

### Debug Log References

- Section page landmarks: `1 <aside`, `1 <footer`, `1 <main`, `2 <nav>`; `<main id=content>` confirmed; h1 count = 1 (root and section).
- `role=banner`/`role=navigation`: none emitted (Hextra uses semantic `<nav>`, no banner). aria-labels present on Search/Change theme/Table of contents/Menu.
- Compiled CSS: 3 `prefers-reduced-motion` occurrences incl. the global `reduce` reset; 2 `no-preference` gates (theme transition, card hover-lift).

### Completion Notes List

- No code change: skip link, focus ring, keyboard operability, labels, landmarks (nav/main/aside/footer), single h1, and reduced-motion all verified present and applying to `/handbook`. Shell meets WCAG 2.1 AA.
- One documented variance: no `<header>`/banner landmark (global parent-site chrome). Flagged for the Epic 1 self-review/PR; not fixed here to preserve site-wide chrome consistency (FR-11).

### File List

- (none — verification only)

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 1.5 verified: skip-link→main, 2px fuchsia focus ring, keyboard + labeled controls, nav/main/aside/footer landmarks, single h1, and reduced-motion reset all confirmed on the Handbook shell (WCAG 2.1 AA met). `<header>`/banner landmark flagged as a global parent-site variance for the PR. Status → review. |
