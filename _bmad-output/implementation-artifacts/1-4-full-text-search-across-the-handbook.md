---
baseline_commit: 854299c6b4c64768ee2b897076e8de1e8d978433
---

# Story 1.4: Full-text search across the Handbook

Status: review

## Story

As a reader,
I want to find a Handbook page by keyword from the site search,
so that I can jump straight to what I need.

## Acceptance Criteria

1. **Given** the site search (header ⌕ / ⌘K), **When** I search a term present in a Handbook page's title, headings, or body, **Then** that page is returned in the results.
2. **Given** Hextra FlexSearch configuration (`index=content`, `maxSectionResults=1`), **When** a single page matches, **Then** it returns as one result, not one result per heading.
3. **Given** a search result, **When** I select it, **Then** it deep-links into the corresponding `/handbook/...` page.

*(Note: fully exercised once topics exist in Epic 2; here verified against the section index pages.)*

## Tasks / Subtasks

- [x] **Task 1 — Confirm Handbook content is indexed (AC: #1)**
  - [x] Verify the built FlexSearch index (`public/en.search-data.json`) contains the Handbook pages with their body content.
- [x] **Task 2 — Confirm one-result-per-page + deep-link (AC: #2, #3)**
  - [x] Verify the index is keyed by page URL (deep-link target) and that `index=content` + `maxSectionResults=1` (inherited config) collapses a page to a single result.
- [x] **Task 3 — Build clean (AC: all)**
  - [x] `hugo --gc --minify` emits the search index with Handbook entries; no config change required.

## Dev Notes

### This is a verification story — no code change

Hextra FlexSearch is already enabled and tuned in `hugo.toml` from the parent-site build (`[params.search] enable=true, type=flexsearch`, `[params.search.flexsearch] index=content, maxSectionResults=1`). Because `index=content` indexes all rendered pages, the Handbook section pages scaffolded in Story 1.2 are indexed automatically — no per-section config. The header ⌕ / ⌘K control (Story 1.3 of the parent) already searches site-wide, including `/handbook`.

### Verification evidence (build output)

- `public/en.search-data.json` is a dict keyed by page URL. It now holds **12 handbook entries** (`/handbook/the-transition/` … `/handbook/engineering-foundations/`) — up from 0 after Story 1.1 (the section intros from Story 1.2 are the indexed content). Each entry's `data` carries the page's intro text → keyword search over title/body returns the page (AC1).
- Keys ARE the `/handbook/{slug}/` permalinks, so selecting a result deep-links to that page (AC3).
- Page content sits under a single `data[""]` bucket; with `maxSectionResults=1` a matching page returns once, not per-heading (AC2). This matters more in Epic 2 when topics carry multiple H2/H3; the cap is already in force.

### Scope note

Section index pages have no sub-headings yet, so the per-heading collapse can't be visually exercised until topics land (Epic 2). The interactive ⌘K type→result→navigate flow is exercised end-to-end during UAT (Story 3.2). For Epic 1 the bar (per the epic's own note) is: Handbook pages are present in the index, keyed by deep-link URL, under the single-result config — all confirmed.

### Guardrails

- No change to `hugo.toml` search config (inherited, correct). FlexSearch is the one allowed JS exception (AD-13). `handbook` branch; no files modified by this story.

### References

- [Source: epics.md#Story-1.4, #FR-7]
- [Source: carriekroutil-site/hugo.toml] — `[params.search.flexsearch] index="content", maxSectionResults=1`
- [Source: build output] — `public/en.search-data.json` with 12 `/handbook/*` keys

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m]

### Debug Log References

- `python3` inspection of `public/en.search-data.json`: top-level dict, 18 keys total, **12 keyed `/handbook/{slug}/`**; sample value `{"data":{"":"AI is reshaping how software gets built…"}}` (building-with-ai intro) — content indexed, URL is the key.

### Completion Notes List

- No code change: FlexSearch config inherited and correct; Handbook indexed automatically once Story 1.2 added content. Verified index presence, URL-keying (deep-link), and single-result config. Full interactive exercise deferred to Epic 2 topics + Story 3.2 UAT, per the epic's own scope note.

### File List

- (none — verification only)

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 1.4 verified: Handbook section pages are present in the FlexSearch index (`en.search-data.json`), keyed by deep-link URL, under inherited `index=content` + `maxSectionResults=1`. No code change. Status → review. |
