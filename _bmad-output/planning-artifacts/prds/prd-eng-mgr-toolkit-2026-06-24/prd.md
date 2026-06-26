---
title: Engineering Manager Handbook
status: final
created: 2026-06-24
updated: 2026-06-25
---

# PRD: Engineering Manager Handbook
*Working title — confirm.*

## 0. Document Purpose

This PRD is for Carrie (PM + author + builder) and the downstream BMAD workflows (UX, architecture, epics/stories) that will turn it into a shipped handbook. It builds directly on the finalized **Product Brief** (`../briefs/brief-eng-mgr-toolkit-2026-06-24/brief.md`) and its **addendum** (full IA map, wiki→section migration mapping, and Hugo/Hextra/Amplify tech constraints) — it does not duplicate them. Features are grouped with globally-numbered FRs nested; assumptions are tagged inline and indexed in §9. Vocabulary is fixed in the Glossary (§3). Implementation specifics (theme as Hugo Module, Amplify build spec, etc.) live in the brief addendum and the forthcoming architecture doc, not here.

## 1. Vision

The Engineering Manager Handbook is an opinionated, practitioner-authored guide to engineering leadership, published as a **Handbook** documentation section of [carriekroutil.com](https://carriekroutil.com). It reshapes years of Carrie's collected wisdom — today trapped in a flat, unsearchable GitHub wiki — into a handbook that works two ways: a guided **Start Here** path for new and aspiring managers, and a fast, searchable **topic reference** for busy leaders mid-problem.

Its edge is the three-legged blend most resources keep apart — **people leadership + hands-on tooling + engineering foundations (the "must-knows")** — curated with judgment rather than dumped as links. It ships next week with its full structure live (real content where it exists, honest stubs elsewhere) and expands in place.

Published under Carrie's own name rather than as a standalone product, it compounds her personal brand — the proven reputation playbook of a consistent personal site (Larson, Orosz, Fournier) — and over time seeds talks, posts, and coaching curricula. The **dual-mode structure itself is the moat**: a guided path *and* a quick reference in one place is something the landscape doesn't offer — the differentiator is better-organized judgment, not more content.

## 2. Target User

### 2.1 Jobs To Be Done

- **Carrie (user-zero):** "Give me one trustworthy place to re-find the framework or tactic I know I've used before, without digging through bookmarks or the old wiki." Also: "Give me something on my own brand I'm proud to share."
- **The new / aspiring manager:** "I just stepped (or am about to step) into management — tell me what to read first and what I don't know I don't know, without a firehose."
- **The experienced leader:** "I need the specific thing fast — a refresher on 1:1 structures, the levers, an a11y primer — found in seconds."
- **The bootcamp / self-taught engineer:** "Name and demystify the foundations nobody taught me (i18n, a11y, on-call) and point me where to learn them for real."

### 2.2 Non-Users (v1)

- People seeking certification, accredited courses, or 1:1 mentorship/coaching (the handbook *points to* those via Go Deeper; it is not one).
- Teams wanting a private/internal company handbook — this is public and personal-brand-anchored.

### 2.3 Key User Journeys

- **UJ-1. Carrie re-finds the levers in ten seconds.**
  Carrie, mid-planning-debate, remembers a framing she wrote up but not where. On carriekroutil.com she hits the search hotkey, types "levers," and the scope/resources/time topic surfaces as the top result. She lands on the page, copies the framing, and is back in the meeting. **Climax:** the answer was one search away, not a bookmark archaeology dig. **Edge case:** if search returns several hits, the topic she wants ranks first because the page title and headings match.

- **UJ-2. Jordan, newly promoted, starts at the beginning.**
  Jordan, a strong senior engineer promoted to manager three weeks ago and feeling underwater, gets the handbook link from Carrie (his coach). He lands on the **Start Here** page, sees an ordered path — *The Transition → Managing Yourself → leading people → …* — and reads the first two in order. Each ends with a "Go Deeper" shortlist; he bookmarks one book. **Climax:** he leaves with orientation and a next step, not 40 open tabs. **Resolution:** he returns over the following weeks, moving down the path and dipping into reference topics as real situations hit.

- **UJ-3. Sam drops in from a search engine.**
  Sam, an experienced director, googles "inclusive interviewing checklist," lands deep-linked on the Hiring & Team Building topic, gets the gist plus a vetted Go Deeper list, and leaves. He never saw the landing page and didn't need to. **Edge case:** the page is a stub — but its intro frames the topic and the Go Deeper block still gives Sam three credible sources, so the visit isn't wasted.

## 3. Glossary

- **Handbook** — the entire Engineering Manager Handbook, published under the `/handbook` path of carriekroutil.com.
- **Section** — one of the 12 top-level groupings in the Handbook sidebar (e.g., *Hiring & Team Building*).
- **Topic page** (or **Topic**) — a single content page within a Section.
- **Start Here** — the Handbook landing page (`/handbook`); presents the guided, ordered path for newcomers. Not a sidebar Section.
- **Guided path** — the recommended reading order through selected Topics, surfaced on Start Here.
- **Topic reference** — the full sidebar tree of Sections/Topics, used for lookup and search.
- **Go Deeper** — the curated block ending every Topic page: book suggestions, courses, and tools.
- **Stub** — a Topic page that is live and navigable but whose body is not yet fully written; carries an intro, a status indicator, and a real Go Deeper block.
- **Migrated Topic** — a Topic whose body is ported from an existing wiki page (see addendum mapping).
- **Hextra docs mode** — the documentation layout of the Hextra theme (left sidebar tree + scoped full-text search), as distinct from its blog layout.
- **Go-live** — the launch where the complete Handbook IA is deployed to production at carriekroutil.com/handbook.

## 4. Features

### 4.1 Handbook integration & navigation

**Description:** The Handbook is a new documentation area at `/handbook` on carriekroutil.com, rendered in Hextra docs mode with a persistent left sidebar tree of the 12 Sections in fixed order. A top-level **Handbook** entry is added to the site's primary header nav. Existing site areas (blog, About, etc.) are unaffected; the Handbook is additive and visually consistent with the rest of the site. Realizes UJ-2, UJ-3.

**Functional Requirements:**

#### FR-1: Handbook section at /handbook
A visitor can reach the Handbook at `carriekroutil.com/handbook` and from a **Handbook** item in the site header.
**Consequences (testable):**
- The header nav shows a Handbook entry on every page; activating it loads the Start Here landing page.
- All Handbook Topics live under the `/handbook/...` path.
- Blog, About, Projects, Experience, search, and theme toggle continue to work unchanged.

#### FR-2: Sidebar tree of 12 Sections in fixed order
On any Handbook page, a visitor can navigate the full Section/Topic tree via the sidebar.
**Consequences (testable):**
- Sidebar lists exactly these Sections, top to bottom: The Transition (Engineer ⇄ Manager); Managing Yourself; People & Leadership; Performance & Growth; Hiring & Team Building; Team Health & Operations; Delivery & Execution; Operational Excellence; Tools & Productivity; Building with AI; Security & Governance; Engineering Foundations.
- The current Topic is highlighted; Sections expand to show their Topics.
- Every Topic is reachable from the sidebar (no orphan pages).

### 4.2 Start Here landing page (guided path)

**Description:** `/handbook` is a landing page, not a content stub: it welcomes newcomers and presents the **Guided path** — an ordered, annotated route through the fundamentals — alongside a clear "or jump to any topic / search" affordance for everyone else. This is how the Handbook serves both audiences from one front door. Realizes UJ-2.

**Functional Requirements:**

#### FR-3: Guided path on Start Here
A new manager can follow a recommended reading order from the Start Here page.
**Consequences (testable):**
- Start Here presents an ordered list of linked Topics forming the path, each with a one-line "why this / what you'll get."
- Path order (confirmed): The Transition → Managing Yourself → People & Leadership → Performance & Growth → Hiring & Team Building, then "branch by what you're facing" into the remaining Sections.
- The page also offers an explicit "experienced? jump to the reference or search" path so non-newcomers aren't funneled through the journey.

### 4.3 Topic page content model

**Description:** Every Topic page follows one consistent anatomy so the Handbook feels coherent and stubs never feel broken: a short framing intro (what this is / why it matters), the body (full for migrated/written Topics, "coming soon" framing for stubs), and a **Go Deeper** block. Pages show a "last updated" date and, for stubs, a status indicator. Realizes UJ-1, UJ-3.

**Functional Requirements:**

#### FR-4: Consistent Topic anatomy
A reader sees the same page structure on every Topic.
**Consequences (testable):**
- Each Topic renders: title, framing intro, body, and a Go Deeper block (in that order).
- Each Topic displays a "Last updated" date.
- Topics with multiple headings render an automatic "On this page" table of contents (Hextra docs default), keeping long Topics navigable without splitting them.

#### FR-5: Go Deeper block on every Topic
A reader gets curated next steps at the end of every Topic, including stubs.
**Consequences (testable):**
- Every published Topic (including every Stub) ends with a Go Deeper block containing at least one curated item (book, course, or tool) with a link.
- `[ASSUMPTION]` Go Deeper items may be grouped by type (Books / Courses / Tools) when more than ~3 exist.

#### FR-6: Stub treatment
A reader landing on a not-yet-written Topic still gets value and understands its state.
**Consequences (testable):**
- A Stub shows a visible status indicator (a "🚧 Expanding" badge or note) and a framing intro, plus its Go Deeper block.
- A Stub is never a 404 and never an empty page; it is fully navigable and searchable.
- Stubs carry no "draft" exclusion that hides them from the published site.

### 4.4 Search & discovery

**Description:** The Handbook is searchable via the site's existing full-text search, scoped so Handbook Topics are findable. Discovery relies on the **sidebar hierarchy + search** — the documentation norm, confirmed against Hextra docs mode — not tags. Realizes UJ-1, UJ-3.

**Functional Requirements:**

#### FR-7: Full-text search across the Handbook
A reader can find a Topic by keyword from the site search.
**Consequences (testable):**
- Searching a term present in a Topic's title/headings/body returns that Topic.
- A single Topic returns as one result (not one per heading).

**Deferred (post-v1):** Tags / taxonomy for cross-cutting discovery. Tags are a blog convention, not a docs one — Hextra docs mode navigates by sidebar hierarchy + search. The site's existing blog keeps its `tags` taxonomy; only the Handbook (docs) section forgoes tags — no conflict. Revisit only if cross-cutting discovery proves needed. *(FR-8 intentionally retired; FR numbering preserved for stable references.)*

### 4.5 Content migration & restructure

**Description:** The 14 existing wiki pages are restructured and migrated into the 12-Section IA per the addendum mapping; gaps and net-new Topics launch as Stubs. Content is edited for the new structure and voice, not copied verbatim. Source content is the cloned `eng-mgr-toolkit` wiki.

**Functional Requirements:**

#### FR-9: Migrate existing wiki content into the IA
Existing substantial wiki pages appear as Migrated Topics in their mapped Sections.
**Consequences (testable):**
- Each substantial wiki page (per the addendum's migration table — the authoritative per-page source) is placed in its target Section, with content ported and headings adapted to the new IA.
- Content that spans Sections (e.g., Tips & Tricks) is split to its correct homes rather than duplicated.
- Existing useful external links are preserved (broken links flagged for cleanup — see Open Questions).

#### FR-10: Complete IA with Stubs at go-live
At go-live, the full Section/Topic structure exists with no missing nav entries.
**Consequences (testable):**
- Every Section has at least its intended v1 Topics present as Migrated Topics or Stubs.
- Net-new Sections (The Transition, Managing Yourself, Hiring & Team Building, Delivery & Execution, Operational Excellence, Building with AI) and new Foundations must-knows (i18n, a11y, video localization) are present at least as Stubs with Go Deeper.

### 4.6 Brand & theming

**Description:** The Handbook is visually indistinguishable in brand from the rest of carriekroutil.com — same Hextra theme, fuchsia primary, dark-default with toggle, typography, and footer. No bespoke UI is introduced; "interactive" comes from Hextra docs features (search, sidebar, code-copy, callouts, tabs, mermaid).

**Functional Requirements:**

#### FR-11: Brand consistency with carriekroutil.com
A visitor moving between the blog and the Handbook perceives one site.
**Consequences (testable):**
- The Handbook uses the same theme, color tokens (fuchsia primary), dark-default + toggle, and header/footer as the rest of the site.
- No custom application or off-brand UI is introduced; brand overrides (if any) go through the existing `assets/css/custom.css`.

### 4.7 Build, deploy & analytics

**Description:** The Handbook ships through the site's existing single AWS Amplify pipeline to production and is measured with the existing privacy-friendly GoatCounter analytics. Standard discoverability (sitemap, robots, meta) extends to Handbook pages.

**Functional Requirements:**

#### FR-12: Ships via the existing production pipeline
The Handbook deploys to production with the rest of the site, no separate infra.
**Consequences (testable):**
- A merge to the site's default branch builds and deploys the Handbook to carriekroutil.com/handbook.
- No second repo, domain, or pipeline is introduced (lives in `carriekroutil-site`).

#### FR-13: Analytics & discoverability
Carrie can see which Topics get traffic; search engines can index the Handbook.
**Consequences (testable):**
- GoatCounter records Handbook page views in production only (no cookies/PII).
- Handbook pages appear in the sitemap and are crawlable (indexable robots policy).

## 5. Non-Goals (Explicit)

- Not a course platform, LMS, certification, or mentorship service.
- Not a community: no comments, accounts, ratings, forums, or user-generated content.
- Not a newsletter or gated/paid content.
- Not a custom interactive web app — no bespoke widgets/calculators in v1.
- Not a separate product: no separate repo, domain, or deploy pipeline.
- Not an exhaustive encyclopedia — opinionated curation over completeness.

## 6. MVP Scope

### 6.1 In Scope
- `/handbook` section on carriekroutil.com in Hextra docs mode, brand-matched (FR-1, FR-2, FR-11).
- Start Here landing page with the guided path + jump-to/search affordance (FR-3).
- Consistent Topic anatomy with a Go Deeper block on every Topic, including Stubs (FR-4, FR-5, FR-6).
- Full-text search across the Handbook (FR-7).
- Migration & restructure of the 14 wiki pages into the 12 Sections; complete IA with Stubs for gaps (FR-9, FR-10).
- Ships via existing Amplify pipeline with GoatCounter + sitemap/robots (FR-12, FR-13).

### 6.2 Out of Scope for MVP
- Fully-written bodies for every Topic — Stubs are expected and expand in place post-launch. `[NOTE FOR PM]` the substantial existing pages (per the addendum migration table — Coaching, Inclusive Teams, Belonging, Strategy, Communicate, Automation Tools, Tips & Tricks, Development Lifecycle, Technical Upskilling) should be genuinely good at launch; only the gaps ship as Stubs.
- Tags / taxonomy — deferred; not a docs convention (sidebar + search suffice). Revisit only if cross-cutting discovery is needed.
- Interactive tools/calculators (e.g., a levers widget) — deferred.
- Per-Topic contribution/feedback mechanisms — deferred.
- Newsletter / RSS for the Handbook specifically — deferred.

## 7. Success Metrics

*Right-sized to a passion project; mostly qualitative + low-effort GoatCounter signals.*

**Primary**
- **SM-1**: Carrie's go-to — she uses the Handbook instead of the old wiki/bookmarks; the old wiki is retired within ~1 month of go-live. Validates FR-1, FR-7.
- **SM-2**: Coachee value — Jordan (the coached manager) is pointed to it next week and reports it useful (orientation via Start Here, answers via search). Validates FR-3, FR-7.

**Secondary**
- **SM-3**: It's live and shareable on carriekroutil.com, brand-consistent, nothing broken in nav. Validates FR-2, FR-10, FR-11.
- **SM-4**: Low-effort signals via GoatCounter — return visits, which Topics draw traffic, search usage — used to prioritize which Stubs to expand. Validates FR-13.

**Counter-metrics (do not optimize)**
- **SM-C1**: Don't chase pageview *growth* — this is a reference/brand asset, not a traffic funnel. Counterbalances SM-4.
- **SM-C2**: Don't trade voice/judgment for coverage — more Stubs is not better; an unopinionated link-dump would betray the differentiator. Counterbalances SM-1.
- **SM-C3**: Don't let Stub count balloon without real Go Deeper value — a Stub with no useful curation is worse than no page. Counterbalances FR-10.

## 8. Open Questions

1. **Broken-link / outdated-content audit** — the wiki has image embeds and external links; do we audit/fix during migration or flag-and-defer? (Carry into migration.)
2. **Per-Topic granularity for very large pages** — default is keep-as-one (auto TOC handles in-page nav); split into multiple Topics only where a sub-part merits its own URL/search hit. Finalize per-topic during UX/migration.

*Resolved during PRD review: guided-path order (confirmed, FR-3); tags (deferred — not a docs convention); stub indicator ("🚧 Expanding" badge, FR-6); long-page handling (auto TOC, FR-4); working title "Engineering Manager Handbook" with nav label "Handbook."*

## 9. Assumptions Index

*Confirmed during PRD review and folded in: guided-path order (FR-3), Go Deeper grouping (FR-5), stub "🚧 Expanding" badge (FR-6), WCAG 2.1 AA target, tags deferred (FR-8). Remaining open assumptions:*

- §6 — Substantial existing wiki content is launch-quality; only true gaps ship as Stubs. (Confirm during migration.)
- Cross-Cutting NFRs — the Go Deeper block is implemented as a reusable convention (likely a Hextra shortcode / template) — architecture to decide.

---

## Information Architecture

**Front door:** `/handbook` = Start Here (guided path + jump/search). **Reference:** sidebar tree of 12 Sections (fixed order below). Full Section→Topic seeds and the wiki→Section migration map live in the brief addendum.

1. The Transition (Engineer ⇄ Manager) · 2. Managing Yourself · 3. People & Leadership · 4. Performance & Growth · 5. Hiring & Team Building · 6. Team Health & Operations · 7. Delivery & Execution · 8. Operational Excellence · 9. Tools & Productivity · 10. Building with AI · 11. Security & Governance · 12. Engineering Foundations *(last — deepest)*

**The Transition (Engineer ⇄ Manager)** covers the identity shift in *both* directions — not just IC→manager, but the honest, healthy move back to IC (the "pendulum swing back"). Treating manager→IC as a legitimate choice rather than a failure is part of the handbook's opinionated edge; the section carries a dedicated "Going back to IC" Topic (launches as a Stub with Go Deeper).

**Security & Governance** = the security standards & standards orgs an EM should know (OWASP, NIST) + compliance literacy (SOC 2, ISO 27001, GDPR) — *not* org/team structures.

Detailed nesting/grouping within Sections is a UX-phase deliverable (see Open Questions, §8).

## Aesthetic & Tone

- **Visual:** identical to carriekroutil.com — Hextra theme, fuchsia primary, dark-default + toggle, same type/header/footer. The Handbook should read as "the same site, a new wing."
- **Voice:** practical, warm, direct, opinionated; first-person where natural ("Pro Tip" asides welcome). Curated-with-judgment, never a neutral link dump. Equal parts heart (leadership) and hands-on craft (tooling/foundations).
- **Anti-references:** unopinionated "awesome-list" READMEs; dense corporate handbook tone; walls of text without a "so what."

## Cross-Cutting NFRs

- **Accessibility:** the site itself should meet a credible bar (WCAG 2.1 AA target, confirmed) — fitting, given a11y is a Handbook topic. Semantic headings, alt text on images, sufficient contrast in both themes.
- **Performance:** statically generated; pages should load fast on the existing Amplify/CDN setup (target: Lighthouse performance ≥ 90 on a typical Topic) with no heavy client JS beyond Hextra defaults.
- **Discoverability/SEO:** per-Topic titles + meta descriptions; sitemap + crawlable robots; clean human-readable `/handbook/...` URLs.
- **Privacy:** analytics remain cookieless/PII-free (GoatCounter), production-only — no consent banner introduced.
- **Maintainability:** authoring a Topic is plain Markdown + front matter; the Go Deeper block is a simple, repeatable convention (`[ASSUMPTION]` likely a shortcode or section template — architecture to decide).
