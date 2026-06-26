---
baseline_commit: 854299c6b4c64768ee2b897076e8de1e8d978433
---

# Story 1.6: Pipeline-ready build on the handbook branch

Status: review

## Story

As Carrie,
I want the Handbook to build green on the `handbook` branch and be ready to ship through the existing pipeline,
so that go-live is just a merge with no infra surprises.

## Acceptance Criteria

1. **Given** all Handbook work is on the `handbook` branch (never `main`), **When** I run `hugo --gc --minify`, **Then** the build succeeds with no errors, **And** no new dependency is added (`HUGO_VERSION` and `go.mod` are unchanged).
2. **Given** the deploy envelope, **Then** no new pipeline, branch preview, or environment is introduced (inherited single Amplify pipeline, `main`-only, previews off).
3. **Given** the discoverability and performance guardrails, **Then** `/handbook/{section}/` URLs are clean and human-readable, **And** the Handbook is not excluded from the inherited sitemap or analytics, **And** pages carry no heavy client JS beyond Hextra defaults (search is the one allowed JS exception).

## Tasks / Subtasks

- [x] **Task 1 — Branch + clean build + no new deps (AC: #1)**
  - [x] Confirm on `handbook` branch (never `main`); `hugo --gc --minify` exits 0 with no errors; `go.mod`/`go.sum`/`HUGO_VERSION` unchanged vs `main`.
- [x] **Task 2 — Deploy envelope unchanged (AC: #2)**
  - [x] Confirm `amplify.yml` is unchanged — no new pipeline, branch preview, or environment.
- [x] **Task 3 — URLs, sitemap, analytics, JS (AC: #3)**
  - [x] Confirm clean `/handbook/{section}/` URLs; Handbook in the sitemap; analytics not path-excluded; no heavy client JS beyond Hextra defaults + FlexSearch.

## Dev Notes

### Verification + guardrail story — closes Epic 1's "ships through the existing pipeline" promise

This story adds no code; it proves the Epic 1 footprint is pipeline-safe. Evidence from the working tree + the production build (`hugo --gc --minify` runs in production mode, so prod-only includes are exercised):

- **Branch (AC1):** `git branch --show-current` → `handbook`. Working-tree changes are exactly: `M hugo.toml`, `M assets/css/custom.css`, `?? content/handbook/` (the 13 new pages). Nothing on `main`.
- **Clean build (AC1):** `hugo --gc --minify` exits 0; 26 pages, 0 errors/warnings introduced.
- **No new dependency (AC1):** `git diff HEAD -- go.mod go.sum amplify.yml` is empty; `HUGO_VERSION: 0.163.3` and Hextra `v0.12.3` unchanged. Local builds used Hugo 0.151.2+extended (above the 0.146.0 floor); prod stays pinned at 0.163.3.
- **Deploy envelope (AC2):** `amplify.yml` untouched → inherited single Amplify pipeline, `main`-only deploy, branch previews off. No new infra.
- **Clean URLs (AC3):** `/handbook/`, `/handbook/{section}/` — derived from the content tree (AD-4), human-readable, no query strings or hashes.
- **Sitemap (AC3):** `public/sitemap.xml` contains 13 `/handbook/*` `<loc>` entries (root + 12 sections) → not excluded; crawlable.
- **Analytics (AC3):** `custom/analytics.html` is gated by `hugo.IsProduction` + `params.goatcounter` only — **not** path-gated — so `//gc.zgo.at/count.js` emits on Handbook pages in production (verified: present in the prod build's handbook output), cookieless/PII-free. (FR-13 is verified in prod in Epic 3; Epic 1's job is just to not exclude it — confirmed.)
- **No heavy JS (AC3):** Handbook page scripts = `main-head.min.js` + `main.min.js` (Hextra defaults) + `en.search…js` / `flexsearch…js` (the one allowed JS exception, AD-13) + `count.js` (async analytics). Inline `<script>` count matches `/about` (5 = 5) → no new client JS introduced.

### Guardrails

- `handbook` branch only; nothing merged/deployed (go-live is the single `main` merge in Epic 3). No module edits (AD-5), no new dependency (AD-10).

### References

- [Source: epics.md#Story-1.6, #FR-12, #NFR-2, #NFR-3, #NFR-4]
- [Source: carriekroutil-site/amplify.yml] — `HUGO_VERSION: 0.163.3`, single pipeline
- [Source: layouts/_partials/custom/analytics.html] — prod+param gate, not path-gated
- [Source: build output] — `public/sitemap.xml` (13 handbook locs), handbook page script set

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m]

### Debug Log References

- `git status --short`: `M assets/css/custom.css`, `M hugo.toml`, `?? content/handbook/`. `git diff --stat HEAD -- go.mod go.sum amplify.yml`: empty.
- `hugo --gc --minify`: exit 0, 26 pages. `public/sitemap.xml`: 13 `/handbook/` locs.
- Handbook page script srcs: `//gc.zgo.at/count.js`, `/en.search.*.js`, `/js/flexsearch.*.js`, `/js/main-head.min.*.js`, `/js/main.min.*.js`. Inline `<script>` parity handbook=about=5.

### Completion Notes List

- No code change: confirmed the Epic 1 footprint (one nav entry, the handbook content tree, one CSS block) is pipeline-ready — clean build, no new deps, untouched deploy envelope, clean URLs, sitemap + analytics inclusion, no heavy JS. Ready to ship via the inherited Amplify pipeline on the eventual `main` merge.

### File List

- (none — verification only)

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 1.6 verified: `handbook` branch builds clean with `hugo --gc --minify`, no new dependency, deploy envelope untouched, clean `/handbook/{section}/` URLs, Handbook in sitemap + analytics, no heavy JS beyond Hextra + FlexSearch. Status → review. |
