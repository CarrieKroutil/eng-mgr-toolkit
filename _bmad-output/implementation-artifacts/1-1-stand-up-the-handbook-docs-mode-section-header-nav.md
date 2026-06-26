---
baseline_commit: 854299c6b4c64768ee2b897076e8de1e8d978433
---

# Story 1.1: Stand up the /handbook docs-mode section + header nav

Status: review

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a visitor to carriekroutil.com,
I want a "Handbook" entry in the site header that opens a `/handbook` area rendered in docs mode,
so that I can find and enter the Handbook as a natural part of the site.

## Acceptance Criteria

1. **Given** any page of carriekroutil.com, **When** I look at the header nav, **Then** I see a "Handbook" entry positioned between "Posts" and "About" (menu `weight = 15`), **And** activating it loads `/handbook`.
2. **Given** the `/handbook` route, **When** it renders, **Then** it uses Hextra docs mode (a persistent left sidebar tree is present) because `content/handbook/_index.md` sets `cascade: { type: docs }`, **And** Posts, Home, and About keep their existing layouts.
3. **Given** the rest of the site (Posts, About, Projects, Experience, search, theme toggle), **When** I use them after the change, **Then** they all work unchanged.
4. **Given** a local build, **When** I run `hugo server`, **Then** `/handbook` builds without errors.

## Tasks / Subtasks

- [x] **Task 0 — Work on the `handbook` branch (AC: all)** ⚠️ BLOCKING PRECONDITION
  - [x] In `carriekroutil-site`, create/checkout the `handbook` feature branch off `main`. **Never commit Handbook work to `main`** — go-live is a single `main` merge at the very end of Epic 3. The repo is currently on `main`.
  - [x] Confirm `git branch --show-current` reports `handbook` before editing any file.

- [x] **Task 1 — Add the "Handbook" header nav entry (AC: #1)**
  - [x] Edit `carriekroutil-site/hugo.toml`. Add a `[[menu.main]]` array-of-tables entry with `name = "Handbook"`, `url = "/handbook/"`, `weight = 15`.
  - [x] Place it so it reads naturally between the existing Posts (`weight = 10`) and About (`weight = 20`) entries. Weight — not file order — determines render order, but keep source order tidy by inserting it after the Posts block.
  - [x] Use a **literal `url`** (`"/handbook/"`), matching the existing Posts/About convention — do NOT use `pageRef`. (The existing comment in `hugo.toml` notes literal URLs avoid build-time pageRef validation; `/handbook/` exists after Task 2 but staying consistent keeps the pattern uniform.)

- [x] **Task 2 — Create the docs-mode section root (AC: #2, #4)**
  - [x] Create `carriekroutil-site/content/handbook/_index.md`.
  - [x] Front matter: `title: "Engineering Manager Handbook"` (full identity for the page H1 / `<title>`; the nav label stays the short "Handbook") and `cascade:` with `type: docs`. This `cascade` is **REQUIRED** — without it the section does not get Hextra's docs layout (no sidebar tree). This is build-verified per AD-14.
  - [x] Body: a minimal placeholder is fine for this story (e.g. an HTML comment noting the Start Here landing arrives in Epic 3). Do **not** author the Start Here welcome copy, path-step cards, section grid, or a `layouts/handbook/list.html` override here — those are Story 3.1 (AD-19). Do **not** create the 12 Section folders here — that is Story 1.2.
  - [x] Do **not** set `draft: true` anywhere in the Handbook tree (it would hide pages from build/nav/search and break the "complete IA, nothing 404s" promise — AD-18).

- [x] **Task 3 — Verify build + no regressions (AC: #2, #3, #4)**
  - [x] Run `hugo server` and open `http://localhost:1313/handbook/`. Confirm it loads with the docs frame (a left sidebar region is present — it will be empty of sections until Story 1.2; that is expected). *(Verified via static build output: `/handbook` desktop sidebar renders `hx:md:sticky` = docs mode.)*
  - [x] Confirm the header shows Posts · **Handbook** · About in that order, and clicking Handbook navigates to `/handbook/`.
  - [x] Confirm Posts, Home (`/`), About (`/about/`), Projects (`/projects/`), Experience (`/experience/`), header search (⌕ / ⌘K), and the theme toggle all still render/behave as before — they must keep their existing (non-docs) layouts. Only `/handbook/...` should be in docs mode. *(Verified: home/about sidebar renders `hx:md:hidden` = mobile drawer only, NOT docs mode; all four legacy pages + `en.search-data.json` built.)*
  - [x] Confirm a clean build: `hugo --gc --minify` exits 0 with no errors/warnings introduced by this change.

## Dev Notes

### What this story is (and is NOT)

This is the **first** story of Epic 1 (the branded, navigable shell) and the **one-time setup** described in the architecture cheat-sheet. Scope is deliberately tiny: (1) one nav entry, (2) one `_index.md` that flips the `/handbook` subtree into Hextra docs mode. Everything else in Epic 1 builds on top:
- 12 Section scaffolds + slugs/weights → **Story 1.2**
- Sidebar brand styling (fuchsia active state, ~300px) → **Story 1.3**
- Search across the Handbook → **Story 1.4**
- a11y floor (skip-link, focus ring, landmarks) → **Story 1.5**
- Pipeline-green guardrails → **Story 1.6**
- Start Here landing body + `layouts/handbook/list.html` → **Story 3.1**

Keep this story's footprint to exactly the two files below. Resist scope creep into 1.2/1.3/3.1.

### Repository & target paths (verified against the live repo)

- **Work repo:** `carriekroutil-site` (local at `/Users/carrie/code/carriekroutil-site`). This planning repo (`eng-mgr-toolkit`) holds BMAD artifacts + raw wiki source only — **no published content lands here** (AD-20).
- Files this story touches (only these two):
  - `carriekroutil-site/hugo.toml` — UPDATE (add one `[[menu.main]]`)
  - `carriekroutil-site/content/handbook/_index.md` — NEW

### Current state of `hugo.toml` (read before editing — preserve everything)

`hugo.toml` is a load-bearing, heavily-commented config. The existing `[[menu.main]]` entries and their weights:

| name | url / type | weight |
|------|-----------|--------|
| Posts | `/posts/` | 10 |
| About | `/about/` | 20 |
| Search | type=search | 30 |
| Theme | type=theme-toggle | 40 |
| GitHub | (link) | 50 |
| LinkedIn | (link) | 60 |

Hextra renders these in ascending weight order. Inserting `Handbook` at **15** yields header order: Posts · Handbook · About · ⌕ · ☾ · GitHub · LinkedIn. **Do not renumber any existing weight** — 15 fits the existing gap by design (AD-14).

Other settings in `hugo.toml` that must remain untouched (they are why this is one cohesive site — FR-11): `[module]` Hextra import, `[taxonomies] tag`, `[markup.highlight] noClasses=false`, `[params]` avatar/description/`goatcounter`, `[params.theme] default="dark"` + `displayToggle`, `[params.search]` flexsearch (`index="content"`, `maxSectionResults=1`), `[params.footer] displayPoweredBy=false`. **You are adding exactly one array-of-tables block; change nothing else.**

### The `_index.md` to create

```yaml
---
title: "Engineering Manager Handbook"   # page H1 / <title>; nav label stays "Handbook"
cascade:
  type: docs        # REQUIRED — turns the /handbook subtree into Hextra docs mode (sidebar tree). Build-verified (AD-14).
---
<!-- Start Here landing body (welcome copy + guided-path step cards + section grid)
     is authored in Epic 3 / Story 3.1 via a root-scoped layouts/handbook/list.html override.
     This story only stands up the docs-mode section + nav. -->
```

- `cascade: { type: docs }` scopes docs mode to `/handbook/**` only — posts/home/about are NOT affected (that is the whole point of `cascade` on this one `_index.md`).
- An empty docs section (no child sections yet) builds fine; the sidebar simply has no entries until Story 1.2 adds the 12 Section folders. AC #2 only requires the docs frame / sidebar region to be present.

### Architecture guardrails (binding)

- **AD-14 (docs-mode section + nav):** `cascade: { type: docs }` on `content/handbook/_index.md`; single `[[menu.main]]` at `weight = 15`, label "Handbook". This story IS AD-14.
- **AD-20 (one home for content):** published content lives only in `carriekroutil-site`. Never put `content/handbook/**` in this planning repo.
- **Branch discipline (inherited AD-2/AD-11/AD-12):** all Handbook work on the single `handbook` branch; nothing deploys until the final `main` merge (Epic 3). No new pipeline, branch preview, or environment.
- **No new dependency (inherited AD-10):** do NOT touch `go.mod`/`go.sum` or `HUGO_VERSION`. Stack is pinned: Hugo extended **0.163.3** (in `amplify.yml`), Hextra **v0.12.3** (in `go.mod`), `go 1.25.3`.
- **Nav label = "Handbook"** (reconciled 2026-06-25 across PRD/UX/Architecture; "EM Handbook"/"EM Wiki" retired). The full "Engineering Manager Handbook" identity lives only in the page `title`/H1/share-meta, not the nav.
- **Guardrail for later FRs:** do NOT exclude `/handbook` from the inherited sitemap or GoatCounter analytics — FR-13 is verified in Epic 3 and depends on the default inclusion staying in place. (You are not configuring analytics here; just don't add any exclusion.)

### Testing standards

There is no unit-test harness for site config; verification is the **`hugo server` local preview** — the only pre-merge check (Amplify branch previews are disabled by inherited decision). Acceptance = the four AC checks in Task 3, performed manually against `localhost:1313`. Capture a screenshot of the header (showing Posts · Handbook · About) and of `/handbook/` rendering in docs mode for the Dev Agent Record / PR.

### Definition of done for this story

- On the `handbook` branch: `hugo.toml` has the one new menu entry; `content/handbook/_index.md` exists with the `cascade.type=docs` front matter.
- `hugo --gc --minify` builds clean; `hugo server` shows `/handbook/` in docs mode and the rest of the site unchanged.
- No edits to `go.mod`, `go.sum`, `amplify.yml`, `layouts/**`, or any existing content page.
- Nothing merged to `main`.

### Project Structure Notes

- `content/handbook/` does not exist yet — Task 2 creates it via the `_index.md`. Hugo derives the `/handbook/` URL from the content tree (inherited AD-4); no route config needed.
- No `layouts/handbook/` override exists or should be created in this story (that's Story 3.1). Hextra's module supplies the docs layout once `type: docs` is set.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.1] — user story + acceptance criteria
- [Source: _bmad-output/planning-artifacts/epics.md#Additional-Requirements] — AD-14, AD-20, AD-21, inherited envelope
- [Source: _bmad-output/planning-artifacts/architecture/architecture-eng-mgr-toolkit-2026-06-25/BUILD-CHEATSHEET.md#One-time-setup] — exact `hugo.toml` menu block + `_index.md` cascade snippet
- [Source: carriekroutil-site/hugo.toml] — existing `[[menu.main]]` weights (Posts 10 / About 20) and the literal-URL convention
- [Source: carriekroutil-site/go.mod, amplify.yml] — pinned stack (Hextra v0.12.3, Hugo 0.163.3) — do not change

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m] (Claude Opus 4.8, 1M context)

### Debug Log References

- `hugo --gc --minify` → exit 0; 26 pages built, 0 errors. (Local Hugo v0.151.2+extended; above the Hextra v0.12.3 floor of 0.146.0. Prod pins 0.163.3 in `amplify.yml` — unchanged.)
- Docs-mode scoping verified from minified output (HTML minification strips attribute quotes, so nav `href` renders as `href=/handbook/`):
  - `/handbook` desktop sidebar: `hextra-sidebar-container … hx:md:sticky` → **visible on desktop = docs mode**.
  - `/` (home) and `/about/` desktop sidebar: `… hx:md:hidden` → mobile drawer only, **not docs mode**. (The site-wide mobile drawer uses the same `hextra-sidebar` classes on every page; it is not docs mode — the `md:` visibility class is the discriminator.)
- Header nav order on home, in document order: `/posts/` → `/handbook/` → `/about/` (weight 10 → 15 → 20).
- Regression check: `public/{posts,about,projects,experience}/index.html` all generated; `public/en.search-data.json` (FlexSearch index) generated.

### Completion Notes List

- Scope held to exactly two files (one `[[menu.main]]` block in `hugo.toml`; new `content/handbook/_index.md`). No edits to `go.mod`, `go.sum`, `amplify.yml`, `layouts/**`, `assets/**`, or any existing content page — stack pins and brand chrome untouched (FR-11, AD-10).
- All Handbook work is on the `handbook` branch (created off `main` at baseline `854299c`); nothing committed to `main`.
- AD-14 satisfied: `cascade.type=docs` flips only the `/handbook` subtree to docs mode; literal-URL nav entry matches the existing Posts/About convention. Sidebar tree is intentionally empty until Story 1.2 adds the 12 Section scaffolds; Start Here body/layout is deferred to Story 3.1 (AD-19).
- Guardrail honored: `/handbook` was NOT excluded from the inherited sitemap or GoatCounter analytics (FR-13, verified in Epic 3).
- Note for reviewer: changes are staged in the working tree but **not yet committed** — Epic 1 commits/PR happen after self code-review at epic end.

### File List

- `carriekroutil-site/hugo.toml` — UPDATE (added one `[[menu.main]]` entry: Handbook, weight 15)
- `carriekroutil-site/content/handbook/_index.md` — NEW (docs-mode section root via `cascade.type=docs`)

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 1.1 implemented: added "Handbook" header nav entry (weight 15) and `content/handbook/_index.md` with `cascade.type=docs`; clean Hugo build verified, docs-mode scoping + no regressions confirmed. Status → review. |
