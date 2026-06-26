---
baseline_commit: d4eba960674376f81f697fae112dd1793e204024
---

# Story 3.1: Build the Start Here landing (guided path)

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a new manager,
I want a Start Here landing with an ordered guided path,
so that I get oriented without a firehose.

## Acceptance Criteria

1. **Given** `/handbook` (root `_index.md`), **When** it renders, **Then** a root-scoped `layouts/handbook/list.html` override shows the approved welcome copy → numbered path-step cards → a section grid → the inherited footer, in a single centered column (sidebar still present; the three-column docs frame deliberately broken).
2. **Given** the layout override, **Then** it is scoped to the `/handbook/` root index only, **And** the 12 Section `_index.md` pages keep Hextra's default docs section-list.
3. **Given** the guided path, **Then** the path-step cards are ordered The Transition → Managing Yourself → People & Leadership → Performance & Growth → Hiring & Team Building, each with a one-line "why", **And** each card links via Hugo `relref` (build-failing if a target is missing).
4. **Given** a non-newcomer, **Then** an explicit "experienced? jump to the reference or search" affordance is present, **And** there is no on-page search box (the header ⌘K already covers Handbook pages).
5. **Given** the About nudge, **Then** it reads "Written and curated by Carrie Kroutil — […]. More about me →" and links to `/about`.
6. **Given** the build, **When** `hugo` runs, **Then** it builds green with all `relref` targets resolving.

## Tasks / Subtasks

- [x] **Task 0 — Work on the `handbook` branch (AC: all)** ⚠️ BLOCKING PRECONDITION
  - [x] In `carriekroutil-site`, checkout the existing `handbook` feature branch (it already holds Epics 1–2; current HEAD = `d4eba96`). **Never commit Handbook work to `main`** — go-live is the single `main` merge in Story 3.3.
  - [x] Confirm `git branch --show-current` reports `handbook` before editing any file.

- [x] **Task 1 — Create the root-scoped Start Here layout override (AC: #1, #2)**
  - [x] Create `carriekroutil-site/layouts/handbook/list.html` (NEW). This is the AD-19 override.
  - [x] ⚠️ **Root-guard (the AD-19 trap):** this template matches the `/handbook/` root index **and all 12 Section index pages** (every page under `content/handbook/` has `.Section == "handbook"` and `Kind == "section"`, so the lookup is identical). The template MUST branch: when the page is the root (`{{ if eq .RelPermalink "/handbook/" }}` — or equivalently `eq .File.Dir "handbook/"`), render the Start Here body; **else** delegate to Hextra's default docs section-list so the 12 Section indexes render exactly as they do today. Verify the 12 section pages are visually unchanged (this is AC #2 and the primary regression risk).
  - [x] Render the Start Here body in a **single centered column** (`--ck-reading-max`-style width, ~760px), sidebar still present, the three-column docs frame deliberately broken (DESIGN.md §Layout). Order: **welcome (H1 + lede) → experienced-jump affordance → eyebrow + 5 path-step cards → branch line → eyebrow + 12-card section grid → About nudge → inherited footer.**

- [x] **Task 2 — Author the welcome copy + About nudge (AC: #1, #5)**
  - [x] Render the approved H1 + lede (verbatim in Dev Notes → "Approved copy").
  - [x] Render the About nudge after the grid, **approved verbatim** (Carrie, 2026-06-25): "Written and curated by Carrie Kroutil — an engineering leader sharing what she's learned in the trenches. More about me →", with "More about me →" (or the whole line) linking to `/about`.
  - [x] Prose (lede, About nudge) MAY live as markdown in `content/handbook/_index.md` body (rendered via `.Content`) for easy editing, or directly in the template — dev's choice. Recommended: prose in `_index.md` body, structure (cards/grid/eyebrows) in the template.

- [x] **Task 3 — Build the guided-path step cards with `relref` (AC: #3, #6)**
  - [x] Five numbered path-step cards in this exact order, each linking to the Section index via `relref` (build-failing on a missing target — AD-22): `the-transition`, `managing-yourself`, `people-leadership`, `performance-growth`, `hiring-team-building`.
  - [x] Each card: step number (1–5) + Section name (bold) + the one-line "why" (verbatim in Dev Notes). Whole card is the link (`<a>`), with a visible focus ring.
  - [x] Use the `relref` function form in the template: `{{ relref . "/handbook/the-transition" }}` (or the `{{< relref >}}` shortcode if authored in markdown). Path is **content-root-absolute** — it MUST include the `/handbook/` prefix (a bare `the-transition` resolves against `content/` and fails with `REF_NOT_FOUND`).
  - [x] Render the italic branch line under the cards (verbatim in Dev Notes).

- [x] **Task 4 — Build the 12-section grid with `relref` (AC: #1, #6)**
  - [x] A grid of 12 compact cards (one per Section, in weight order 1–12), each linking to its Section index via `relref`. Card: zero-padded number (01–12) + Section name + a short tagline (verbatim taglines in Dev Notes).
  - [x] Prefer iterating the actual Section pages (ordered by `weight`) for the names/links so they stay in sync; the bespoke taglines are not on the section pages, so supply them from the verbatim list (a template data slice keyed by slug, or hardcoded in card order).

- [x] **Task 5 — Add the experienced-jump affordance, no on-page search box (AC: #4)**
  - [x] Add an explicit lightweight "experienced? jump to the reference or search" affordance near the top (after the lede). It points experienced users at the sidebar (the 12-Section reference, always present) and the header search (⌘K). It is a text link/sentence, **not** a search input.
  - [x] Do **NOT** add an on-page search box or a search-jump button (header ⌘K already covers Handbook pages). See the EXPERIENCE-vs-AD-19 reconciliation note in Dev Notes.

- [x] **Task 6 — Style the Start Here components (AC: #1)**
  - [x] Add new CSS for the centered column, path-step cards, branch line, eyebrows, section grid, and About nudge to `carriekroutil-site/assets/css/custom.css`. (No equivalent classes exist yet — the existing handbook CSS covers sidebar / topic anatomy / stub badge / Go Deeper / Pro Tip only.)
  - [x] **No new color values** (FR-11, AD-17): reuse the existing `--ck-*` tokens (`--ck-accent-fuchsia/violet/amber/gradient`, `--ck-radius-card`, etc.). Use the gradient only as a sparing garnish (step numbers / footer accent), never to fill cards.
  - [x] Make accent text **theme-aware** (DESIGN.md): bright tints in dark mode, the `html:not(.dark)` light companions for light mode — the eyebrow fuchsia and any accent text must pass AA on white. Verify in the light theme before sign-off.
  - [x] Honor `prefers-reduced-motion`: card hover-lift transitions drop under reduced motion (match the existing pattern in `custom.css`).

- [x] **Task 7 — Verify build + no regressions (AC: #1–#6)**
  - [x] `hugo --gc --minify` exits 0 with no new errors/warnings; all 17 `relref` targets (5 path + 12 grid) resolve (a missing one fails the build — that is the AD-22 contract working).
  - [x] `hugo server` → `http://localhost:1313/handbook/` shows the Start Here landing (centered column, sidebar present, cards + grid + About nudge + footer).
  - [x] **Regression (AC #2):** visit all 12 section indexes (`/handbook/the-transition/` … `/handbook/engineering-foundations/`) and confirm each renders Hextra's default docs section-list, unchanged — the override did NOT leak onto them.
  - [x] Verify in **both** dark and light themes (accent contrast); keyboard-tab through cards/links (visible focus rings); confirm there is no on-page search box and the About nudge links to `/about`.
  - [x] Confirm no edits to `go.mod`, `go.sum`, `amplify.yml`, or `hugo.toml`; nothing merged to `main`.

## Dev Notes

### What this story is (and is NOT)

This is the **first** story of Epic 3 and the **last build story** before UAT (3.2) and go-live (3.3). It delivers FR-3 (the Start Here guided path / AD-19). It builds on the now-complete shell (Epic 1) and full IA (Epic 2) — which is *why* it comes last: the guided-path `relref` cards only resolve once the Section pages exist (AD-22 is build-failing).

In scope (exactly these files):
- `layouts/handbook/list.html` — **NEW** (root-scoped Start Here override; AD-19)
- `content/handbook/_index.md` — **UPDATE** (replace the placeholder comment with the welcome/About prose, if you author prose in content; front matter stays as-is)
- `assets/css/custom.css` — **UPDATE** (new Start Here component styles, existing tokens only)

NOT in scope: the 12 Section `_index.md` files (do not edit — they must keep Hextra's default rendering), any topic file, `hugo.toml`, `go.mod`/`go.sum`, `amplify.yml`. No new dependencies, no new colors, no second pipeline.

### Repository & target paths (verified against the live `handbook` branch @ `d4eba96`)

- **Work repo:** `carriekroutil-site` (local at `/Users/carrie/code/carriekroutil-site`), branch **`handbook`**. This planning repo (`eng-mgr-toolkit`) holds BMAD artifacts + raw wiki source only — **no published content lands here** (AD-20).
- Existing structure that matters:
  - `content/handbook/_index.md` — exists; front matter = `title: "Engineering Manager Handbook"` + `cascade: { type: docs }`; body is the Story-1.1 placeholder comment (replace it).
  - Layouts use Hugo's `_partials`/`_default` convention. There is one existing handbook layout: `layouts/docs/single.html` (the Topic template, Epic 2). There is **no** `layouts/handbook/list.html` and **no** `layouts/docs/list.html` or `layouts/_default/list.html` in the project — the 12 Section indexes currently render via the **Hextra module's** docs templates. Adding `layouts/handbook/list.html` overrides the module for *all* handbook-section pages → hence the root-guard (Task 1).
  - Handbook partials live in `layouts/_partials/custom/` (e.g. `go-deeper.html`); shortcodes in `layouts/shortcodes/` (e.g. `protip.html`). Reuse where helpful; you do not need them for Start Here.
  - Custom CSS: `assets/css/custom.css`. Handbook block ≈ lines 1141–1311; light-theme companions ≈ lines 1073–1138. Tokens already defined: `--ck-accent-fuchsia #d946ef`, `--ck-accent-violet #8b5cf6`, `--ck-accent-amber #f59e0b`, `--ck-accent-gradient`, `--ck-sidebar-w 300px`, `--ck-reading-max 720px` (handbook topics use 760px via `.ck-handbook-topic`), `--ck-radius-card 18px`, `--ck-radius-pill 999px`.

### ⚠️ The AD-19 root-scoping trap (most important detail)

`cascade: { type: docs }` on the root applies `type=docs` to the whole `/handbook` subtree, and **every** page under `content/handbook/` has `.Section == "handbook"`. So Hugo's layout lookup for the root `/handbook/_index.md` AND for each of the 12 Section `_index.md` pages resolves to the **same** template — `layouts/handbook/list.html`. If the override renders Start Here unconditionally, it **leaks onto all 12 Section indexes** (AD-19's named failure mode). Conversely, with no override the root renders as a bare/empty docs section-index.

**Required:** branch inside `layouts/handbook/list.html`:
- Root (`eq .RelPermalink "/handbook/"`) → render the Start Here body.
- Otherwise → render Hextra's default docs section-list (delegate, so the 12 sections are untouched). Choose the cleanest delegation that reproduces today's output (e.g. fall through to the Hextra docs list partial/template, or define the override to short-circuit only the root). **Verification, not theory, is the gate:** after wiring this, open all 12 section indexes and confirm they look identical to `main`/pre-change.

`relref` is build-failing by Hugo default (`refLinksErrorLevel = ERROR`) — do not lower it; that is the AD-22 guarantee.

### Approved copy (verbatim — from the approved mockup `start-here.html`)

> Visual reference: `_bmad-output/planning-artifacts/ux-designs/ux-eng-mgr-toolkit-2026-06-24/mockups/start-here.html`. The mockup is the contract-illustrating mock; **spines win on conflict**.

**H1:** `The Engineering Manager Handbook`

**Welcome copy (UAT 2026-06-25 — replaced the mockup lede; public-facing adaptation of the brief Executive Summary + the brief's Problems reframed as the handbook's goals, then rewritten in Carrie's own voice to match her blog/About — first-person, plain-spoken, no marketing register; DRAFT pending Carrie's sign-off):**
- Single paragraph (UAT-final): `This is the handbook I wish someone had handed me when I started leading engineering teams. It's the stuff I've picked up over the years — managing people, the hands-on tooling, and the engineering foundations nobody really teaches you but you're expected to know — all in one place. Every topic ends with my favorite picks for going deeper, so there's always a next step waiting.`
- (The earlier two-paragraph version with the dual-mode "I built it to work two ways…" + "Bonus:" sentence was collapsed at Carrie's request: para 2 dropped, its closing "Every topic ends…" sentence appended to para 1. Navigation guidance is still carried by the experienced-jump line + the "New to management?" eyebrow.)
- (Earlier "opinionated practitioner's guide" draft rejected by Carrie as off-voice; superseded mockup lede kept for reference: `Notes from the trenches on engineering leadership — the people, the tooling, and the foundations nobody hands you. New to management? Start at step one. Here for one thing? Jump straight to it.`)

**Eyebrow (before path):** `New to management? Start here →`

**Path-step cards (order + bold title + one-line why):** *(UAT 2026-06-25 — Carrie trimmed the guided path to the first 3 cards; #4 Performance & Growth and #5 Hiring & Team Building removed. All 12 still appear in the section grid below, so their `relref` targets remain referenced.)*
1. **The Transition (Engineer → Manager)** — `Is this the right move? What changes, and your first 90 days.` → `relref "/handbook/the-transition"`
2. **Managing Yourself** — `Time, energy, boundaries — keep your own tank full first.` → `relref "/handbook/managing-yourself"`
3. **People & Leadership** — `What the job actually is: leading, communicating, inclusive teams.` → `relref "/handbook/people-leadership"`
4. **Performance & Growth** — `1:1s, feedback, coaching, reviews, growing your people.` → `relref "/handbook/performance-growth"`
5. **Hiring & Team Building** — `Interview inclusively, onboard well, build the team.` → `relref "/handbook/hiring-team-building"`

**Branch line (italic, under the cards):** `…then branch by what you're facing — delivery, on-call, AI, security, or the engineering must-knows.`

**Eyebrow (before grid):** `Browse all 12 sections`

**Section grid (number · name · tagline), weight order, each → `relref` to its Section index:**
| # | Section | Tagline | relref |
|---|---------|---------|--------|
| 01 | The Transition | Engineer → Manager | `/handbook/the-transition` |
| 02 | Managing Yourself | Sustain & grow | `/handbook/managing-yourself` |
| 03 | People & Leadership | Lead & communicate | `/handbook/people-leadership` |
| 04 | Performance & Growth | 1:1s, feedback, reviews | `/handbook/performance-growth` |
| 05 | Hiring & Team Building | Recruit & onboard | `/handbook/hiring-team-building` |
| 06 | Team Health & Operations | Health, ceremonies, lifecycle | `/handbook/team-health-operations` |
| 07 | Delivery & Execution | Scope, resources, time | `/handbook/delivery-execution` |
| 08 | Operational Excellence | On-call, monitoring, incidents | `/handbook/operational-excellence` |
| 09 | Tools & Productivity | Automation & tips | `/handbook/tools-productivity` |
| 10 | Building with AI | Literacy & AI-native eng | `/handbook/building-with-ai` |
| 11 | Security & Governance | OWASP, NIST, compliance | `/handbook/security-governance` |
| 12 | Engineering Foundations | i18n, a11y, the must-knows | `/handbook/engineering-foundations` |

**Experienced-jump affordance (AC #4):** no verbatim mockup line exists; author a short on-brand sentence/link, e.g. *"Been here before? Skip the path — browse the full reference in the sidebar, or hit ⌘K to search."* Points to the sidebar + header search; **no on-page search input**.

**About nudge (AC #5) — APPROVED VERBATIM (Carrie, 2026-06-25; trimmed during UAT):** `Written and curated by Carrie Kroutil. More about me →` — "More about me →" links to `/about`. (The earlier "— an engineering leader sharing what she's learned in the trenches" clause was removed at Carrie's request.)

**Footer:** the inherited site footer (do not author a bespoke one — reuse the inherited `layouts/_partials/custom/footer.html` / Hextra footer so the page ends like every other page).

### EXPERIENCE-vs-AD-19 reconciliation (don't get tripped up)

EXPERIENCE.md §IA Surface 1 says Start Here has "**No on-page search box or jump button**." AD-19 (Architecture, dated 2026-06-25) and Story AC #4 require an explicit "experienced? jump to the reference or search" **affordance**. These reconcile as: **no on-page search input box and no search *button*** (the ⌘K header search covers it) — **but yes** a lightweight escape-hatch link/sentence pointing experienced readers to the sidebar reference + ⌘K. The spine (AD-19) wins on conflict. Implement the affordance; do not add a search box.

### Architecture guardrails (binding)

- **AD-19 (Start Here overrides the docs frame, root-scoped):** this story IS AD-19. Single centered column, sidebar present, three-column frame broken; override scoped to the root only (the trap above).
- **AD-22 (internal links via `relref`, build-failing):** all 17 path + grid links use `relref` with the `/handbook/`-prefixed content-root-absolute path. No hand-typed `/handbook/...` href strings for internal links.
- **AD-5 (customization in project `layouts/` + `assets/`, never the Hextra module):** the override is a project layout; CSS in `assets/css/custom.css`. Never edit the vendored module.
- **AD-17 / FR-11 (no new colors / one cohesive brand):** reuse `--ck-*` tokens only; gradient as sparing garnish.
- **AD-10 (no new dependency):** do NOT touch `go.mod`/`go.sum` or `HUGO_VERSION`. Stack pinned: Hugo extended 0.163.3 (`amplify.yml`), Hextra v0.12.3 (`go.mod`).
- **Branch discipline (inherited):** all work on `handbook`; nothing deploys until the Story 3.3 `main` merge. No new pipeline/preview/environment.
- **FR-13 guardrail:** do not exclude `/handbook` from the inherited sitemap or GoatCounter — Story 3.4 verifies analytics + crawlability in prod and depends on the default inclusion staying in place.

### Accessibility (NFR-1, target WCAG 2.1 AA)

- One `<h1>` (the handbook title) on the page; eyebrows are styled non-heading text, not `Hn` (don't disturb heading order / TOC).
- Cards are real `<a>` elements with visible focus rings; logical tab order; keyboard-operable.
- Landmarks intact: the Start Here body lives inside `<main>`; `header`/`nav`(sidebar)/`footer` inherited from the shell (the Epic 1 a11y floor — skip-link, landmarks — must keep working; don't break it by replacing the frame wholesale).
- Theme-aware accent text passes AA in **both** themes (light companions for white backgrounds). Verify in light mode.
- Honor `prefers-reduced-motion` (drop card hover-lift).

### Testing standards

No unit-test harness for layouts/CSS; verification is the **`hugo server` local preview** + a clean `hugo --gc --minify` (the only pre-merge check — Amplify branch previews are disabled by inherited decision). Acceptance = the Task 7 checks. Capture screenshots (Start Here in dark + light, and one Section index proving no leak) for the Dev Agent Record / PR.

### Definition of done for this story

- On `handbook`: `layouts/handbook/list.html` renders Start Here at the root only; the 12 Section indexes render Hextra's default unchanged (verified).
- Welcome copy, 5 path-step cards (relref), branch line, 12-card grid (relref), experienced-jump affordance, and About nudge (→ `/about`) all present, in a single centered column, sidebar present, on-brand.
- `hugo --gc --minify` builds clean; all 17 relref targets resolve; verified in dark + light, keyboard-navigable, no on-page search box.
- No new colors; no edits to `go.mod`/`go.sum`/`amplify.yml`/`hugo.toml`/Section `_index.md` files. Nothing merged to `main`.
- About-nudge copy is the approved verbatim line (no placeholder).

### Project Structure Notes

- `layouts/handbook/list.html` is the correct override slot per the spine source-tree map (AD-19). It sits beside the existing `layouts/docs/single.html` (Topic template).
- Putting prose in `content/handbook/_index.md` body (rendered via `.Content`) keeps it markdown-editable for Carrie (NFR-5) while the template owns the cards/grid/structure — recommended but not required.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-3.1] — user story + acceptance criteria
- [Source: _bmad-output/planning-artifacts/architecture/architecture-eng-mgr-toolkit-2026-06-25/ARCHITECTURE-SPINE.md#AD-19] — Start Here overrides the docs frame, root-scoped (the trap)
- [Source: _bmad-output/planning-artifacts/architecture/architecture-eng-mgr-toolkit-2026-06-25/ARCHITECTURE-SPINE.md#AD-22] — internal links via `relref`, build-failing; content-root-absolute paths
- [Source: _bmad-output/planning-artifacts/architecture/architecture-eng-mgr-toolkit-2026-06-25/BUILD-CHEATSHEET.md] — exact section slugs; `relref` usage; `_index.md` setup; no-new-colors
- [Source: _bmad-output/planning-artifacts/ux-designs/ux-eng-mgr-toolkit-2026-06-24/EXPERIENCE.md#Information-Architecture] — Surface 1 Start Here; voice & tone; the "no jump button" line reconciled above
- [Source: _bmad-output/planning-artifacts/ux-designs/ux-eng-mgr-toolkit-2026-06-24/DESIGN.md] — tokens, theme-aware accents, single centered column, path-step-card / section-grid-card specs
- [Source: _bmad-output/planning-artifacts/ux-designs/ux-eng-mgr-toolkit-2026-06-24/mockups/start-here.html] — approved verbatim copy + visual reference
- [Source: carriekroutil-site @ handbook d4eba96] — current `content/handbook/_index.md`, `assets/css/custom.css` tokens, layout structure (no existing `handbook/list.html` or card CSS)

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Claude Opus 4.8, 1M context)

### Debug Log References

- `hugo --gc --minify` → exit 0; 103 pages, 0 errors/warnings. All 17 `relref` targets (5 path + 12 grid + `/about`) resolved — a broken one would have failed the build (AD-22 confirmed working). Local Hugo v0.151.2+extended (prod pins 0.163.3 in `amplify.yml`, unchanged).
- **Lookup-precedence finding (drove the deviation below):** added a temporary `<!--CKHANDBOOK rp={{ .RelPermalink }}-->` marker to a `layouts/handbook/list.html` and rebuilt — the marker appeared on **neither** the root nor any section page, proving the section-named template never runs. Under `cascade {type: docs}`, Hugo resolves handbook section-list pages to the **docs TYPE** slot, and **type beats section** in the lookup order, so Hextra's module `layouts/docs/list.html` wins. The override therefore had to move to the docs `list.html` slot. Marker removed after diagnosis.
- Root render verified from built HTML: `public/handbook/index.html` contains `ck-start-here`, 5 `ck-step` links, 12 `ck-gcard` links, the About link resolves to `/about/`, and there is **no** `hextra-toc` (three-column frame broken, AC #1).
- No-leak verified across all 12 section indexes: each `public/handbook/<slug>/index.html` has `ck-start-here`=0 and `hextra-toc`=1 (theme default intact, AC #2).
- Visual verification via Playwright at `localhost:1313`: Start Here in **dark** + **light** themes (theme-aware fuchsia eyebrows/links AA-safe in both), and a section page (`/handbook/the-transition/`) confirming the normal docs frame. Screenshots saved to the session scratchpad for the PR.

### Completion Notes List

- **Deviation from AC #1 / Task 1 path — implemented at `layouts/docs/list.html`, not `layouts/handbook/list.html`.** The AC's named path is mechanically impossible under `cascade {type: docs}` (type-slot wins over section-slot — see Debug Log). The architecture's own AD-19 *prose* already prescribes the working slot: *"it renders via the **docs `list.html` slot**, so it must guard on the root path."* So `layouts/docs/list.html` satisfies AD-19's intent and all six ACs; it mirrors the existing `layouts/docs/single.html` (the Epic 2 topic override at the same docs slot). The `layouts/handbook/list.html` naming in the epics AC / spine source-tree table is an internal inconsistency — recommend correcting the spine/epics to `layouts/docs/list.html` (flagged for the Epic 3 retro / next architecture touch).
- Root-guard implemented with `{{ if eq .RelPermalink "/handbook/" }}`: root → Start Here; else → a verbatim copy of Hextra's `docs/list.html` body so the 12 Section indexes are byte-for-byte the theme default. Keep the else-branch in sync with the theme on Hextra upgrade (noted in the file header).
- All Start Here copy lives in the layout (not the `_index.md` body): the About nudge must sit *after* the grid, which `.Content` can't express as a single block, so template-authored markup was cleaner. `content/handbook/_index.md` body comment updated to document this and the slot choice; front matter unchanged.
- No new colors (FR-11/AD-17): every Start Here style derives from existing `--ck-*` tokens; the gradient is used only as garnish (step-number discs). Accent text uses the theme-aware `--ck-accent-text` (fuchsia #d946ef dark / #a21caf light) so eyebrows + links pass AA in both themes. Card hover-lift is under `prefers-reduced-motion: no-preference`; focus rings come from the global `:focus-visible` rule (cards are real `<a>`).
- About-nudge copy is the approved verbatim line (Carrie, 2026-06-25); links to `/about` via `relref` (build-failing).
- Scope held to three files; no edits to `go.mod`/`go.sum`/`amplify.yml`/`hugo.toml`/any Section `_index.md`/any topic. All work on the `handbook` branch; nothing committed to `main`. Changes staged in the working tree, not yet committed (Epic 3 commits/PR per the go-live sequence in Stories 3.2–3.3).

### File List

- `carriekroutil-site/layouts/docs/list.html` — NEW (root-scoped Start Here override with root-guard + theme-default delegation; AD-19, AD-22)
- `carriekroutil-site/assets/css/custom.css` — UPDATE (new `.ck-start-*` / `.ck-step*` / `.ck-grid`/`.ck-gcard` styles; existing `--ck-*` tokens only, theme-aware, reduced-motion)
- `carriekroutil-site/content/handbook/_index.md` — UPDATE (body comment now documents the layout slot + root-guard; front matter unchanged)

## Senior Developer Review (AI)

Fresh-context adversarial review (separate agent) on 2026-06-25 against the ACs + architecture rules. **Verdict: Approve-with-nits** — no High/blocking findings. Verified clean: AC coverage (root Start Here, 3 path cards, 12 grid, About → `/about`, single `<h1>`, eyebrows not headings, no TOC on root), no-leak across all 12 section indexes, all 15 internal links via `relref`, no raw colors (all `--ck-*`), theme-aware AA, focus rings via global `:focus-visible`, reduced-motion gating, scope held to 3 files, diagnostic marker removed.

Action items:
- [x] **M1 (Med) — frozen theme copy drift:** the else-branch is a byte-for-byte copy of Hextra's `docs/list.html`; on a Hextra upgrade it won't auto-update and the override wins with no build error. **Addressed:** comment now names the pinned source `github.com/imfing/hextra@v0.12.3` with explicit re-copy/diff instructions.
- [x] **L1 (Low) — root guard coupled to baseURL:** `eq .RelPermalink "/handbook/"` would fall through to the empty docs index under a path-prefix/lang-subdir baseURL. **Addressed:** switched to comparing the page against the section-root page object (`$root := .Site.GetPage "/handbook"`; `eq . $root`) — immune to URL shape; rebuilt + re-verified root renders + no leak.
- [x] **L2 (Low) — hardcoded grid data:** taglines aren't on the section pages, so the grid is hand-maintained; `relref` catches a deleted slug but not a rename/reorder. **Addressed:** added a manual-sync comment by the `$grid` slice. (Kept hardcoded — taglines are bespoke.)
- [ ] **N1 (Nit) — untokenized literals:** `.ck-start-here{max-width:880px}` and `.ck-gcard{border-radius:14px}` are literal px (intentional: landing is wider than the ~720/760px reading column; grid cards are compact). Left as-is; Carrie's call whether to add `--ck-start-max` / reuse `--ck-radius-card`.
- N2 (Nit) — `aria-hidden` on decorative glyphs + real `<kbd>` for ⌘K confirmed correct; no action.

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 3.1 created via create-story (context engine): full Start Here contract — root-scoped `layouts/handbook/list.html` override (AD-19 trap documented), 17 `relref` links (AD-22), verbatim approved copy, theme-aware new CSS on existing tokens, About-nudge `[…]` flagged for UAT. Status → ready-for-dev. |
| 2026-06-25 | About-nudge copy approved by Carrie (drafted default locked); status → in-progress. |
| 2026-06-25 | Story 3.1 implemented on `handbook` branch: Start Here landing at `layouts/docs/list.html` (root-guard + theme-default delegation), 5 path cards + 12-section grid via `relref`, experienced-jump affordance (no search box), approved About nudge → `/about`, new `.ck-start-*` CSS on existing tokens (theme-aware, reduced-motion). **Deviation:** override at `layouts/docs/list.html`, not `layouts/handbook/list.html` — type-slot wins over section-slot under `cascade {type: docs}` (matches AD-19 prose; epics/spine path naming to be corrected). Clean `hugo --gc --minify`; all 17 `relref` resolve; no leak onto the 12 sections; verified dark + light. Status → review. |
| 2026-06-25 | UAT tweak: trimmed the About nudge to "Written and curated by Carrie Kroutil. More about me →" (removed the "engineering leader…" clause). Clean build. |
| 2026-06-25 | UAT: replaced the single mockup lede with two welcome paragraphs — public-facing adaptation of the brief's Executive Summary + Problems-as-goals. Carrie rejected the first "opinionated practitioner's guide" phrasing as off-voice; rewritten in her own first-person voice (matched against her blog/About). DRAFT, awaiting sign-off. Copy saved in Dev Notes → Welcome copy. Clean build. |
| 2026-06-25 | UAT copy polish: dropped "and all in my voice"; rewrote the closing line with a more uplifting/excited tone ("And the best part? … finally spelled out, plain and clear. … my favorite picks for going deeper, so there's always a next step waiting."). Clean build. |
| 2026-06-25 | UAT: dropped para-1 "Not a pile of links…" sentence; trimmed the guided path from 5 cards to 3 (removed Performance & Growth + Hiring & Team Building; section grid still lists all 12). Clean build; 3 path cards confirmed. |
| 2026-06-25 | UAT: shortened para 2 — removed the "And the best part? … finally spelled out, plain and clear." sentence (kept the dual-mode + Go Deeper close). Clean build. |
| 2026-06-25 | UAT: added a "Bonus:" sentence before the Go Deeper close — "quick references on the engineering must-knows, too — so your leadership has both depth and breadth." (Carrie's draft, polished; breath→breadth.) Clean build. |
| 2026-06-25 | UAT: collapsed welcome to a single paragraph — added "but you're expected to know" to the foundations clause; dropped para 2 (dual-mode + Bonus); appended its "Every topic ends…" close to para 1. Clean build. |
| 2026-06-25 | UAT: increased `.ck-start-h1` bottom margin 0.5rem → 1rem for more space between the title and the welcome paragraph. Clean build. |
| 2026-06-25 | Fresh-eyes code review: Approve-with-nits. Addressed M1 (named pinned Hextra @v0.12.3 in the drift warning), L1 (root guard now compares to the section-root page object, not a URL string — durable against path-prefix/lang-subdir baseURLs), L2 (manual-sync comment on the grid data). N1 nits left to Carrie. Rebuilt + re-verified root + no-leak. |
| 2026-06-25 | Committed to `handbook` as `6b438f8` (3 files) and pushed; PR #21 (carriekroutil-site, handbook→main, still DRAFT) now includes Story 3.1. |
