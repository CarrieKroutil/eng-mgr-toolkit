---
baseline_commit: 854299c6b4c64768ee2b897076e8de1e8d978433
---

# Story 1.2: Scaffold the 12 Sections in fixed order with stable slugs

Status: review

## Story

As a Handbook visitor,
I want the sidebar to list all 12 Sections in the curated fixed order,
so that I can navigate the whole handbook structure.

## Acceptance Criteria

1. **Given** the Handbook sidebar, **When** it renders, **Then** it lists exactly these 12 Sections top-to-bottom: The Transition · Managing Yourself · People & Leadership · Performance & Growth · Hiring & Team Building · Team Health & Operations · Delivery & Execution · Operational Excellence · Tools & Productivity · Building with AI · Security & Governance · Engineering Foundations.
2. **Given** each Section folder, **Then** its slug matches the fixed AD-21 map (`the-transition` … `engineering-foundations`), **And** its `_index.md` sets a unique `weight` 1..12 in that order.
3. **Given** section 1, **When** it is shown in the sidebar, **Then** it displays the short label "The Transition" (via `linkTitle`), **And** its section page title remains "The Transition (Engineer ⇄ Manager)".
4. **Given** each Section index page, **Then** it renders a one-paragraph "what this section covers" intro (placeholder acceptable at this stage), **And** it is reachable from the sidebar (no orphan pages).

## Tasks / Subtasks

- [x] **Task 1 — Create 12 Section folders, each with `_index.md` (AC: #1, #2, #4)**
  - [x] Under `carriekroutil-site/content/handbook/`, create one folder per Section using the EXACT slug from the AD-21 map (never re-slug). Each folder gets an `_index.md`.
  - [x] Each `_index.md` front matter: `title`, unique `weight` 1..12 in the fixed order. Add a one-paragraph "what this section covers" intro in the body so the page is non-empty (and indexable by search in Story 1.4). *(Authored real one-paragraph intros, not placeholders.)*
- [x] **Task 2 — Short sidebar label for Section 1 (AC: #3)**
  - [x] Section 1 `_index.md`: `title: "The Transition (Engineer ⇄ Manager)"`, `linkTitle: "The Transition"`, `weight: 1`. (Hugo `linkTitle` is what the Hextra sidebar renders; the full title stays the page H1.)
- [x] **Task 3 — Verify sidebar order, slugs, reachability, build (AC: all)**
  - [x] `hugo --gc --minify` builds clean (exit 0).
  - [x] In the rendered sidebar, all 12 sections appear top-to-bottom in weight order; Section 1 shows "The Transition". *(Verified via aside-region slug extraction: order is the-transition → … → engineering-foundations, 1–12.)*
  - [x] Each `/handbook/{slug}/` page builds and is reachable from the sidebar (no orphans, no 404s). *(All 12 `/handbook/{slug}/index.html` generated; all 12 indexed in `en.search-data.json`.)*

## Dev Notes

### Binding AD-21 Section folder-slug + order map (do NOT re-slug)

| # (weight) | `title` | `linkTitle` | folder slug |
|---|---|---|---|
| 1 | The Transition (Engineer ⇄ Manager) | The Transition | `the-transition` |
| 2 | Managing Yourself | — | `managing-yourself` |
| 3 | People & Leadership | — | `people-leadership` |
| 4 | Performance & Growth | — | `performance-growth` |
| 5 | Hiring & Team Building | — | `hiring-team-building` |
| 6 | Team Health & Operations | — | `team-health-operations` |
| 7 | Delivery & Execution | — | `delivery-execution` |
| 8 | Operational Excellence | — | `operational-excellence` |
| 9 | Tools & Productivity | — | `tools-productivity` |
| 10 | Building with AI | — | `building-with-ai` |
| 11 | Security & Governance | — | `security-governance` |
| 12 | Engineering Foundations | — | `engineering-foundations` |

- Only Section 1 needs `linkTitle` (it's the only label that wraps at the ~260–300px sidebar width). All other labels fit one line — do NOT add `linkTitle` to them.
- `weight` orders sidebar sections (AD-16); each is unique 1..12. Topic-level weights come in Epic 2.
- These are **Section index pages** (`_index.md` = Hugo branch bundles), NOT topics. They keep Hextra's default docs section-list layout — do NOT add `cascade` (it's inherited from the parent `/handbook/_index.md` from Story 1.1), `stub`, or `goDeeper` here (those are topic-level, Epic 2). The build-fails-on-empty-`goDeeper` rule (AD-17) applies to **topics**, not section index pages.
- Body intro: one honest paragraph ("what this section covers"). Real prose is fine and preferred over lorem; it also gives Story 1.4 search something to index.

### Guardrails

- Work on the `handbook` branch only (continues Story 1.1; never `main`).
- Content lives only in `carriekroutil-site/content/handbook/` (AD-20). No `go.mod`/`amplify.yml`/`layouts` changes.
- Builds on Story 1.1 (the `/handbook/_index.md` docs-mode root already exists). Section pages inherit `type: docs` via cascade.

### References

- [Source: _bmad-output/planning-artifacts/epics.md#Story-1.2]
- [Source: .../architecture/.../BUILD-CHEATSHEET.md#One-time-setup] — section `_index.md` shape + slug table
- [Source: epics.md#AD-21, #AD-16] — fixed slugs + weight ordering
- [Source: toolkit-site-decisions memory] — §1 `linkTitle` lever; §12 named "Engineering Foundations"

## Dev Agent Record

### Agent Model Used

claude-opus-4-8[1m]

### Debug Log References

- `hugo --gc --minify` clean (Total in 154 ms, exit 0). All 12 `/handbook/{slug}/index.html` generated.
- Sidebar order verified by extracting `/handbook/{slug}/` links from the desktop `<aside>` region in document order: 1 the-transition → 2 managing-yourself → 3 people-leadership → 4 performance-growth → 5 hiring-team-building → 6 team-health-operations → 7 delivery-execution → 8 operational-excellence → 9 tools-productivity → 10 building-with-ai → 11 security-governance → 12 engineering-foundations.
- §1: page H1 = "The Transition (Engineer ⇄ Manager)"; sidebar/search label = "The Transition" (`linkTitle` honored by Hugo/Hextra).
- Search: `en.search-data.json` now carries 12 handbook entries (was 0 after Story 1.1) — confirms section index pages are non-empty and indexed (unblocks Story 1.4).

### Completion Notes List

- 12 Section branch bundles (`_index.md`) created with exact AD-21 slugs, unique weights 1..12 in fixed order. Only §1 carries `linkTitle` (the one label that wraps at sidebar width); the other 11 fit one line.
- Section index pages intentionally carry NO `cascade`/`stub`/`goDeeper` — `type: docs` is inherited from the Story 1.1 `/handbook/_index.md` cascade; stub/goDeeper are topic-level (Epic 2). Section pages keep Hextra's default docs section-list layout.
- Authored genuine one-paragraph "what this section covers" intros (better than placeholders, and gives search real content).
- On `handbook` branch; no `layouts`/`go.mod`/`amplify.yml` changes; content only under `content/handbook/` (AD-20).

### File List

- `carriekroutil-site/content/handbook/the-transition/_index.md` — NEW (title + linkTitle + weight 1)
- `carriekroutil-site/content/handbook/managing-yourself/_index.md` — NEW (weight 2)
- `carriekroutil-site/content/handbook/people-leadership/_index.md` — NEW (weight 3)
- `carriekroutil-site/content/handbook/performance-growth/_index.md` — NEW (weight 4)
- `carriekroutil-site/content/handbook/hiring-team-building/_index.md` — NEW (weight 5)
- `carriekroutil-site/content/handbook/team-health-operations/_index.md` — NEW (weight 6)
- `carriekroutil-site/content/handbook/delivery-execution/_index.md` — NEW (weight 7)
- `carriekroutil-site/content/handbook/operational-excellence/_index.md` — NEW (weight 8)
- `carriekroutil-site/content/handbook/tools-productivity/_index.md` — NEW (weight 9)
- `carriekroutil-site/content/handbook/building-with-ai/_index.md` — NEW (weight 10)
- `carriekroutil-site/content/handbook/security-governance/_index.md` — NEW (weight 11)
- `carriekroutil-site/content/handbook/engineering-foundations/_index.md` — NEW (weight 12)

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Story 1.2 implemented: scaffolded all 12 Section index pages with AD-21 slugs, unique weights 1–12, §1 `linkTitle`, and authored intros. Sidebar order + slugs + reachability + search indexing verified. Status → review. |
