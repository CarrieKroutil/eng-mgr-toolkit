---
title: UX Spine Completeness & Reconciliation Review
status: review
created: 2026-06-24
reviewer: finalize-time QA pass
scope:
  - DESIGN.md
  - EXPERIENCE.md
  - source PRD (prd-eng-mgr-toolkit-2026-06-24/prd.md)
---

# UX Spine Review — Engineering Manager Handbook

**Overall verdict:** SHIP-READY with minor fixes. The two spines are coherent, well-scoped, and cover their spec sections. Every PRD FR has a UX home. No critical or high findings. One medium (light-mode stub-badge contrast) and a few low/cosmetic items worth a five-minute cleanup before architecture/build. Calibrated to a modest-stakes passion project on a next-week deadline.

**Finding counts:** Critical 0 · High 0 · Medium 1 · Low 4

---

## 1. COVERAGE

### DESIGN.md — all eight sections present
Brand & Style ✓ · Colors ✓ · Typography ✓ · Layout & Spacing ✓ · Elevation & Depth ✓ · Shapes ✓ · Components ✓ · Do's and Don'ts ✓. Components section names all nine new docs-mode components and cross-refs EXPERIENCE for behavior. Tokens are concrete (hex, rem, px). No load-bearing gap for a builder.

### EXPERIENCE.md — all eight sections present
Foundation ✓ · Information Architecture ✓ · Voice and Tone ✓ · Component Patterns ✓ · State Patterns ✓ · Interaction Primitives ✓ · Accessibility Floor ✓ · Key Flows ✓. IA includes the full 12-Section → Topic map with M/S tagging and a closure check. Four key flows map 1:1 to the three PRD UJs plus the stub-expansion authoring loop.

**No load-bearing coverage gaps.** Minor notes below (LOW-1, LOW-2).

---

## 2. PRD COHERENCE

Every active FR has a UX surface/component. FR-8 is intentionally retired in the PRD (numbering preserved); not a gap.

| FR | UX home | Status |
|----|---------|--------|
| FR-1 Handbook at /handbook + header entry | EXPERIENCE Foundation + IA "Global nav delta" (Handbook placed between Posts and About, weight 15); DESIGN brand continuity | Covered |
| FR-2 Sidebar tree of 12 Sections, fixed order | DESIGN docs-sidebar; EXPERIENCE IA Section→Topic map + docs-sidebar behavior | Covered |
| FR-3 Guided path on Start Here | DESIGN path-step-card / section-grid-card; EXPERIENCE Surface 1 + Flow 2 | Covered |
| FR-4 Consistent Topic anatomy + Last-updated + auto-TOC | DESIGN on-this-page-toc, breadcrumbs; EXPERIENCE Topic surface + Content model. **See LOW-1 (Last-updated display).** | Covered (with note) |
| FR-5 Go Deeper on every Topic | DESIGN go-deeper-block; EXPERIENCE component + authoring conventions | Covered |
| FR-6 Stub treatment | DESIGN stub-badge; EXPERIENCE stub state + microcopy | Covered |
| FR-7 Full-text search, one result per Topic | DESIGN inherited search; EXPERIENCE Surface 4 + search behavior (one result per Topic) + Flow 1 | Covered |
| FR-9 Migrate wiki content into IA | EXPERIENCE IA map with M-tagging per addendum | Covered |
| FR-10 Complete IA with Stubs at go-live | EXPERIENCE IA map (S-tagging) + Empty-Section state | Covered |
| FR-11 Brand consistency | DESIGN Brand & Style + Do's/Don'ts ("same site, a new wing") | Covered |
| FR-12 Ships via existing Amplify pipeline | EXPERIENCE Foundation (build system, content/handbook/, push→Amplify) | Covered |
| FR-13 Analytics & discoverability | EXPERIENCE Flow 4 (GoatCounter traffic drives stub priority); `description` front-matter for meta/SEO. **See LOW-2 (sitemap/robots not explicit).** | Covered (with note) |

**No contradictions** between UX elements and the PRD. One alignment worth flagging as resolved-not-contradiction:

- **MEDIUM-adjacent (resolved):** The PRD (FR-3 description, §6.1) says Start Here offers an explicit "jump to any topic / search" affordance. EXPERIENCE Surface 1 deliberately drops an *on-page* search box/jump button, reasoning that the header ⌘K and always-present sidebar already cover it. This is a justified design decision, not a contradiction — the "jump/search" capability still exists (header + sidebar), just not duplicated on the landing page. Spines win on conflict per their own stated rule. **No change needed; noting so architecture doesn't "restore" a redundant search box thinking it's a missing requirement.**

---

## 3. ACCESSIBILITY SANITY

The spines target WCAG 2.1 AA and inherit AA-verified parent tokens. Assessment of the NEW docs-mode elements:

### Contrast (computed)
- **Amber stub-badge text #ffd591 on surface-2 #1c1c23 (dark mode): 12.24:1 — PASS AA (and AAA).** Note: EXPERIENCE estimates "≈10:1"; the actual ratio is **12.24:1**. The claim is correct/conservative — fine to leave, or update the number for precision.
- Stub-badge border #f59e0b on surface-2: 7.89:1 (non-text UI, needs 3:1) — PASS.
- Go Deeper group labels on surface #15151a: Books violet #c9b8ff 10.21:1, Courses fuchsia #f3a8ff 10.27:1, Tools amber #ffd591 13.15:1 — all PASS AA.
- Sidebar-active fuchsia #d946ef on surface #15151a: 5.26:1 — PASS AA for text. (Parent site already verified fuchsia link at 5.71:1 on bg.)

### MEDIUM-1 — Light-mode stub badge fails contrast
EXPERIENCE already flags "re-verify in light mode before shipping," and the math confirms the concern is real: **#ffd591 amber-tint text on a white/near-white light-mode surface = 1.38:1 — FAILS AA badly.** The stub-badge color tokens are defined only for the dark surface. Because the badge color is hard-coded (not a theme-aware token pair), the badge will be near-invisible/illegible in light mode unless the build supplies a darker amber for light theme (e.g., #92600a-ish on white clears 4.5:1). **Action for architecture/build:** make stub-badge-text (and the Go Deeper group-label tints, which have the same single-value risk) theme-aware token pairs, or verify Hextra's light-mode badge styling before go-live. Low effort, real bug if shipped as-is.

### Keyboard / structure / no introduced a11y risks
- **No keyboard traps:** sidebar tree, TOC, search, prev/next are all Hextra built-ins specified as real `<a>`/`<button>` with labels — no custom focus-management widget that could trap. EXPERIENCE Accessibility Floor explicitly calls for logical tab order and keyboard operability.
- **Skip link:** EXPERIENCE specifies a skip-to-content link past the sidebar — good, this is the right call for a sidebar-heavy layout.
- **Heading order:** EXPERIENCE mandates one `<h1>` per page and logical H2/H3 (also drives the auto-TOC). Stub badge is correctly specified as non-interactive/informational (not a link) and sits *near the title* — confirm at build it isn't injected as a heading (it should be a `<span>`/badge, not an `<h-?>`), so it doesn't break heading order. (LOW-3.)
- **Breadcrumbs:** specified as links up; standard, no risk. Recommend `aria-label="Breadcrumb"` on the nav and `aria-current="page"` on the last crumb (Hextra likely handles this — verify). (LOW-4.)
- **Landmarks, reduced-motion, alt-text** all addressed in the Accessibility Floor.

**No keyboard trap, no skip-link gap, no heading-order defect, no contrast failure in the shipped (dark) default.** Only the light-mode badge (MEDIUM-1) is a genuine pre-ship item.

---

## Low-severity / cleanup items

- **LOW-1 — "Last updated" display not specified visually.** FR-4 requires a visible "Last updated" date; EXPERIENCE Content model has `lastUpdated` front-matter and "(or git-derived)," but neither spine specifies *where/how* it renders on the Topic (Hextra has a built-in `lastUpdated` display — likely just enable it). One line in DESIGN/EXPERIENCE would close the loop for the builder.
- **LOW-2 — Sitemap/robots not surfaced in UX.** FR-13 requires sitemap inclusion + crawlable robots. This is correctly architecture/infra territory, not UX, but EXPERIENCE's per-Topic permalink + `description` front-matter is the only UX-side hook. Fine to leave to architecture; noting so it isn't dropped between docs.
- **LOW-3 — Confirm stub badge renders as non-heading inline element** (so it doesn't disrupt the single-h1 / H2-H3 order the auto-TOC depends on).
- **LOW-4 — Breadcrumb a11y attributes** (`aria-label`, `aria-current`) — verify Hextra default or add; trivial.

---

## Summary for downstream (architecture + build)

1. **MEDIUM-1:** Make stub-badge + Go Deeper group-label colors theme-aware (light-mode amber #ffd591 = 1.38:1 fail). Only true pre-ship a11y item.
2. Wire FR-4's "Last updated" to Hextra's built-in display (LOW-1).
3. Keep sitemap/robots (FR-13) on the architecture checklist (LOW-2).
4. The deliberate "no on-page search box on Start Here" is a design decision, not a missing FR — do not re-add.
5. Spines are otherwise complete and internally consistent; build can proceed.
