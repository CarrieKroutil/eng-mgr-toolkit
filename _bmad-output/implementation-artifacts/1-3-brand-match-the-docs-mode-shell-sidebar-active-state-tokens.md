---
baseline_commit: 854299c6b4c64768ee2b897076e8de1e8d978433
---

# Story 1.3: Brand-match the docs-mode shell (sidebar active state + tokens)

Status: review

## Story

As a visitor moving between the blog and the Handbook,
I want the Handbook to look like the same site,
so that it feels like one cohesive brand.

## Acceptance Criteria

1. **Given** a Handbook page, **When** it renders, **Then** it uses the same theme, fuchsia-primary tokens, dark-default + toggle, header, and footer as the rest of carriekroutil.com, **And** no new color values are introduced (overrides live only in `assets/css/custom.css`).
2. **Given** the sidebar, **When** I am on a section or topic, **Then** the current item is marked with the fuchsia link color + a left accent bar and its ancestors are bolded, **And** the sidebar width is ~300px.
3. **Given** the change, **Then** no bespoke or off-brand UI is introduced — only docs-mode chrome (sidebar/TOC) styled to brand.

## Tasks / Subtasks

- [x] **Task 1 — Verify inherited brand (AC: #1, #3)**
  - [x] Confirm Handbook pages already inherit the site theme: header/footer/wordmark, dark default + working toggle, and the fuchsia primary (the `--primary-*` HSL mapping in `custom.css` already drives Hextra's `--hx-color-primary-*`). No new files, layouts, or colors.
- [x] **Task 2 — Brand the docs sidebar active state + width (AC: #2)**
  - [x] In `assets/css/custom.css`, add a scoped Handbook docs-shell block: give `.hextra-sidebar-active-item` a **left accent bar** in `--ck-accent-fuchsia` (via a positioned `::before`, so it doesn't shift text or disturb the rounded active pill).
  - [x] Widen the desktop docs sidebar from Hextra's default `w-64` (256px) to ~300px by overriding `.hextra-sidebar-container` width at the `md` breakpoint. Use a token (`--ck-sidebar-w`).
  - [x] Confirm active-item text is fuchsia and bold (Hextra defaults `text-primary-600` + `font-semibold`, now fuchsia via the primary mapping) — no override needed beyond the accent bar; only add a rule if verification shows a gap. *(Confirmed fuchsia+bold from defaults; only the accent bar + width were added.)*
- [x] **Task 3 — Verify (AC: all)**
  - [x] `hugo --gc --minify` clean; only `custom.css` changed.
  - [x] On a section page, the active sidebar item shows the fuchsia accent bar + fuchsia bold text; sidebar measures ~300px; header/footer/toggle unchanged; no off-brand chrome. *(Verified in compiled bundle: `--ck-sidebar-w:300px`, `@media(min-width:768px){.hextra-sidebar-container{width:var(--ck-sidebar-w)}}`, and `.hextra-sidebar-active-item::before{…background:var(--ck-accent-fuchsia)}` all present; rules sit after the Hextra layer so they win by cascade.)*

## Dev Notes

### What's already inherited (do NOT re-add — AC1/AC3)

`assets/css/custom.css` (parent-site build) already maps the brand onto Hextra and applies site-wide, so Handbook pages inherit for free:
- Fuchsia primary: `--primary-hue/saturation/lightness` (and `.dark` variant) → Hextra computes `--hx-color-primary-*` from these. The sidebar active item uses `text-primary-600` / `bg-primary-400/10` → already fuchsia.
- Dark default + toggle, frosted header, gradient wordmark, footer, `:focus-visible` fuchsia ring, theme transition (reduced-motion-gated) — all present.

So this story is **only** the two docs-sidebar specifics UX-DR4 calls for that Hextra doesn't give by default: the **left accent bar** and the **~300px width**.

### Hextra active-item facts (verified from build output)

- Active sidebar `<a>` carries class `hextra-sidebar-active-item` plus `hx:bg-primary-100 hx:font-semibold hx:text-primary-800 hx:dark:bg-primary-400/10 hx:dark:text-primary-600`. → already bold + fuchsia-tinted; **no left bar**.
- Sidebar container: `hextra-sidebar-container … hx:md:w-64` (256px). Override its width at `min-width:768px`; `custom.css` loads last so an equal-specificity `.hextra-sidebar-container` rule wins by cascade.
- Use `--ck-accent-fuchsia` (#d946ef, decorative bright fuchsia — correct for a non-text accent bar) for the bar. No new hex.

### Guardrails

- `handbook` branch only. Overrides ONLY in `assets/css/custom.css` (AD-5, FR-11) — never edit the Hextra module. No new color values; reuse `--ck-*` tokens.
- Append a clearly-labeled new section to `custom.css` matching the file's existing comment-block style; don't disturb existing rules.

### References

- [Source: epics.md#Story-1.3, #UX-DR4]
- [Source: carriekroutil-site/assets/css/custom.css] — existing token + primary mapping (lines ~8–85), focus ring, theme transition
- [Source: build output] — `hextra-sidebar-active-item` classes + `hx:md:w-64`

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m]

### Debug Log References

- `hugo --gc --minify` clean (144 ms). Compiled bundle `public/css/compiled/main.min.<hash>.css` contains: `--ck-sidebar-w:300px`; `@media(min-width:768px){.hextra-sidebar-container{width:var(--ck-sidebar-w)}}`; `.hextra-sidebar-active-item{position:relative}`; `.hextra-sidebar-active-item::before{content:"";position:absolute;left:0;top:50%;transform:translateY(-50%);height:1.15em;width:3px;border-radius:3px;background:var(--ck-accent-fuchsia)}`.
- Hextra active item classes (unchanged, inherited): `hextra-sidebar-active-item hx:bg-primary-100 hx:font-semibold hx:text-primary-800 hx:dark:bg-primary-400/10 hx:dark:text-primary-600` → already fuchsia + bold via the `--primary-*` mapping.

### Completion Notes List

- Single-file change (`assets/css/custom.css`); appended one labeled "Story 1.3 (Handbook)" block matching the file's comment style. No new colors — accent bar uses existing `--ck-accent-fuchsia`; `--ck-sidebar-w` is a width token.
- Brand inheritance confirmed working (no work needed): fuchsia primary, dark+toggle, header/footer, focus ring, theme transition all already apply to `/handbook`.
- Only docs-chrome touched (sidebar active bar + width). No layouts, no bespoke components, no module edits (AD-5). `handbook` branch.

### File List

- `carriekroutil-site/assets/css/custom.css` — UPDATE (appended Story 1.3 Handbook docs-shell block: `--ck-sidebar-w`, sidebar width override, active-item left accent bar, + PR-review fix: hide the redundant desktop sidebar theme switch)

### Review Follow-ups (PR #21)

- [x] **[Carrie/PR-review] Duplicate theme toggle in the handbook docs sidebar.** Hextra renders a theme switch at the bottom of the docs sidebar, gated by the same `params.theme.displayToggle` as the navbar toggle (so config can't drop one without the other). On `/handbook` (docs sidebar visible on desktop) it duplicated the navbar toggle. Fix: hide the sidebar's bottom switch on desktop (md+) via `.hextra-sidebar-container > [data-toggle-animation]{display:none}` — the navbar toggle (present at all breakpoints) is the single control; mobile drawer + the blog are unaffected. Verified in compiled bundle + build clean.

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 1.3 implemented: docs sidebar widened to 300px + fuchsia left accent bar on the active item; confirmed inherited fuchsia/bold active state + full brand continuity. Tokens only, no new colors. Status → review. |
| 2026-06-25 | PR #21 review fix: removed the duplicate theme switch from the handbook desktop docs sidebar (kept the single navbar toggle). |
