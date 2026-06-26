---
stepsCompleted: [step-01-validate-prerequisites, step-02-design-epics, step-03-create-stories, step-04-final-validation]
inputDocuments:
  - _bmad-output/planning-artifacts/prds/prd-eng-mgr-toolkit-2026-06-24/prd.md
  - _bmad-output/planning-artifacts/architecture/architecture-eng-mgr-toolkit-2026-06-25/ARCHITECTURE-SPINE.md
  - _bmad-output/planning-artifacts/ux-designs/ux-eng-mgr-toolkit-2026-06-24/DESIGN.md
  - _bmad-output/planning-artifacts/ux-designs/ux-eng-mgr-toolkit-2026-06-24/EXPERIENCE.md
  - _bmad-output/planning-artifacts/briefs/brief-eng-mgr-toolkit-2026-06-24/addendum.md
---

# Engineering Manager Handbook - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for the Engineering Manager Handbook, decomposing the requirements from the PRD, the UX Design contract (DESIGN.md + EXPERIENCE.md), the brief addendum (authoritative wiki→Section migration table), and the Architecture spine into implementable stories.

**Definitive nav label:** "Handbook" (reconciled 2026-06-25 across PRD/UX/Architecture per AD-14; "EM Handbook" retired).

## Requirements Inventory

### Functional Requirements

FR-1: A visitor can reach the Handbook at `carriekroutil.com/handbook` and from a **"Handbook"** item in the site header (every page); activating it loads the Start Here landing page. All Handbook topics live under `/handbook/...`. Blog, About, Projects, Experience, search, and theme toggle continue to work unchanged.
FR-2: On any Handbook page, a visitor can navigate the full Section/Topic tree via the sidebar, listing exactly the 12 Sections in fixed order (The Transition → Managing Yourself → People & Leadership → Performance & Growth → Hiring & Team Building → Team Health & Operations → Delivery & Execution → Operational Excellence → Tools & Productivity → Building with AI → Security & Governance → Engineering Foundations). Current topic highlighted; sections expand to show topics; every topic reachable (no orphans).
FR-3: A new manager can follow a recommended reading order from the Start Here page: an ordered list of linked topics, each with a one-line "why this / what you'll get." Path order: The Transition → Managing Yourself → People & Leadership → Performance & Growth → Hiring & Team Building, then "branch by what you're facing." An explicit "experienced? jump to the reference or search" affordance is offered.
FR-4: A reader sees the same page structure on every Topic: title → framing intro → body → Go Deeper block (in that order). Each topic displays a "Last updated" date. Topics with multiple headings render an automatic "On this page" TOC (Hextra docs default).
FR-5: A reader gets curated next steps at the end of every Topic, including stubs. Every published topic (including every stub) ends with a Go Deeper block containing at least one curated item (book, course, or tool) with a link. Items may be grouped Books / Courses / Tools when more than a few exist; a group renders only when populated.
FR-6: A reader landing on a not-yet-written Topic still gets value and understands its state: a visible "🚧 Expanding" status indicator, a framing intro, and its Go Deeper block. A stub is never a 404 and never empty; it is fully navigable and searchable; stubs carry no "draft" exclusion that hides them from the published site.
FR-7: A reader can find a Topic by keyword from the site full-text search; searching a term present in a topic's title/headings/body returns that topic; a single topic returns as one result (not one per heading).
FR-9: Existing substantial wiki pages appear as Migrated Topics in their mapped Sections (per the addendum migration table — the authoritative per-page source). Content is ported and headings adapted to the new IA; content spanning Sections (e.g., Tips & Tricks) is split to its correct homes rather than duplicated; existing useful external links preserved (broken links flagged for cleanup).
FR-10: At go-live, the full Section/Topic structure exists with no missing nav entries. Every Section has at least its intended v1 Topics present as Migrated Topics or Stubs. Net-new Sections (The Transition, Managing Yourself, Hiring & Team Building, Delivery & Execution, Operational Excellence, Building with AI) and new Foundations must-knows (i18n, a11y, video localization) are present at least as Stubs with Go Deeper.
FR-11: A visitor moving between the blog and the Handbook perceives one site: same theme, color tokens (fuchsia primary), dark-default + toggle, header/footer. No custom application or off-brand UI; brand overrides (if any) go through the existing `assets/css/custom.css`.
FR-12: The Handbook deploys to production with the rest of the site, no separate infra. A merge to the site's default branch (`main`) builds and deploys the Handbook to `carriekroutil.com/handbook`. No second repo, domain, or pipeline (lives in `carriekroutil-site`).
FR-13: GoatCounter records Handbook page views in production only (no cookies/PII). Handbook pages appear in the sitemap and are crawlable (indexable robots policy).

*(FR-8 — tags/taxonomy — intentionally retired and deferred post-v1; not a docs convention. FR numbering preserved for stable references.)*

### NonFunctional Requirements

NFR-1 (Accessibility): The site meets WCAG 2.1 AA — semantic headings, alt text on images, sufficient contrast in **both** light and dark themes.
NFR-2 (Performance): Statically generated; pages load fast on the existing Amplify/CDN setup (target: Lighthouse performance ≥ 90 on a typical Topic) with no heavy client JS beyond Hextra defaults.
NFR-3 (Discoverability/SEO): Per-Topic titles + meta descriptions; sitemap + crawlable robots; clean human-readable `/handbook/...` URLs.
NFR-4 (Privacy): Analytics remain cookieless/PII-free (GoatCounter), production-only — no consent banner introduced.
NFR-5 (Maintainability): Authoring a Topic is plain Markdown + front matter; the Go Deeper block is a simple, repeatable convention (a partial/template, not hand-rolled markup).

### Additional Requirements

*Technical invariants from the Architecture spine that shape epics/stories. Inherited parent invariants (AD-1..AD-13) are binding and read-only; new decisions are AD-14..AD-22.*

- **AD-14 (docs-mode section):** `content/handbook/_index.md` sets `cascade: { type: docs }` so only Handbook pages use Hextra's native docs layout (left sidebar tree + on-this-page TOC); posts/home/about untouched. A single `[[menu.main]]` entry is added to `hugo.toml` at `weight = 15` (between Posts=10 and About=20), label **"Handbook"**.
- **AD-15 (canonical topic front-matter contract):** every topic carries YAML front matter — `title`, `weight` (unique within Section), `description` (meta/SEO), optional `stub` (bool, default false), `goDeeper` (list), `lastUpdated` (RFC-3339, hand-authored, rendered under title; never `draft: true` on a stub). Flat file `content/handbook/{section}/{topic}.md` is the default; a page bundle `{topic}/index.md` is permitted only for image-bearing topics (inherited AD-9 alt-text build-fail applies).
- **AD-16 (sidebar order):** each Section `_index.md` sets `weight` 1..12 in the fixed order; topics order by their own `weight` within a Section, unique per Section (no ties); every topic reachable (no orphans).
- **AD-17 (Go Deeper data block):** authored as a `goDeeper` front-matter list (not free-form markdown), per-item schema pinned `{ group, title, url, why }`, `group ∈ {Books, Courses, Tools}` (closed enum; group renders only when populated). Rendered at end of every topic by a project partial in `layouts/`. External links open new tab with `rel="noopener"`. Group labels use existing `--ck-chip-{violet,fuchsia,amber}-text` tokens. **Render fails the build if a topic (incl. a stub) has no `goDeeper` items.**
- **AD-18 (stub state):** `stub: true` is the only stub mechanism; renders an inline "🚧 Expanding" badge (non-heading, never disturbs heading order/auto-TOC, purely informational, not a link). Stub intro is honest framing prose that does not repeat the badge label verbatim. Stub builds/navigates/searches and carries Go Deeper exactly like a full topic.
- **AD-19 (Start Here override):** `content/handbook/_index.md` + a custom layout override (in `layouts/`) renders guided-path step cards + a section grid in a single centered column (sidebar still present; three-column docs frame deliberately broken). Override scoped to the `/handbook/` root index only (via docs `list.html` slot, guarded on root path); the 12 Section `_index.md` pages keep Hextra's default docs section-list. No on-page search box; offers an explicit "experienced? jump to the reference or search" affordance.
- **AD-20 (one home for published content):** authored/published topic markdown lives solely in `carriekroutil-site/content/handbook/` and ships via the inherited single Amplify pipeline. `eng-mgr-toolkit` (this repo) holds BMAD planning artifacts and raw wiki source for migration only. Migration is a one-way port-and-edit, not an ongoing sync.
- **AD-21 (binding Section folder-slug map):** the 12 Section folder slugs are fixed, in order: `the-transition`, `managing-yourself`, `people-leadership`, `performance-growth`, `hiring-team-building`, `team-health-operations`, `delivery-execution`, `operational-excellence`, `tools-productivity`, `building-with-ai`, `security-governance`, `engineering-foundations`. Topic slugs derive from file/folder name (inherited AD-4), human-readable and stable.
- **AD-22 (internal links via relref):** every internal Handbook link — guided-path step cards, section grid, and any topic-to-topic reference — uses Hugo `ref`/`relref` (fails build on missing target). Hand-written `/handbook/...` path strings are not used for internal links.
- **Inherited envelope (binding, read-only):** AD-1 authoring loop is `write markdown → git push` (no CMS); AD-2/AD-11/AD-12 one Amplify pipeline, `main` only, prod-only, build-fails-notify (no new infra/branch-previews/environment); AD-3 YAML front matter; AD-4 URL derives from content tree; AD-5 all customization in project `layouts/`+`assets/` (never the Hextra module); AD-9 images via page bundles with build-failing alt text; AD-10 versions pinned, no new dependency; AD-13 core reading works with JS disabled, FlexSearch is the one allowed JS exception.
- **Branch & launch discipline:** all Handbook work lands on **one feature branch**, never on `main`; nothing deploys until that branch is merged; `hugo server` is the only pre-merge preview. Merge = full go-live (complete IA + stubs).
- **Stack (no new dependency):** Hugo extended `0.163.3` (inherited via `HUGO_VERSION`), Hextra `v0.12.3` (locked in `go.mod`), AWS Amplify + Route 53, Hextra FlexSearch (`index=content`, `maxSectionResults=1`).

### UX Design Requirements

*First-class actionable work items from the UX contract (DESIGN.md + EXPERIENCE.md). Items marked "(Hextra default)" are config/verify, not net-new build.*

UX-DR1 (Go Deeper block component): build the `layouts/_partials/custom/go-deeper.html` partial rendering the `goDeeper[]` front matter as a `surface` card with a "📚 Go Deeper" heading; Books / Courses / Tools groups shown only when populated; each item = title (body-color link, fuchsia on hover) + one-line "why"; group labels carry brand-accent tints (Books = violet, Courses = fuchsia, Tools = amber) via existing `--ck-chip-*` tokens; external links open new tab with `rel="noopener"`; build fails if no items (AD-17).
UX-DR2 (Stub badge component): build the `layouts/_partials/custom/stub-badge.html` rendering an amber "🚧 Expanding" pill from `stub: true`; inline non-heading element (never an `Hn`), purely informational (not a link); on `surface-2` bg with dimmed amber border; theme-aware text (`#ffd591` dark); never red (AD-18).
UX-DR3 (Start Here landing layout): build `layouts/docs/list.html` (root-scoped — the docs `list.html` slot, since `cascade type:docs` routes handbook section-list pages to the docs type slot, which outranks a `layouts/handbook/` section slot in Hugo's lookup; guard on the root path) rendering a single centered column (sidebar present, three-column docs frame broken): a short welcome → numbered **path-step cards** (step number, Section/Topic name, one-line "why," entire card links to the topic via `relref`) → a **section-grid** of compact cards (one per Section, links to the Section index) → inherited footer; explicit "experienced? jump to the reference or search" affordance; **no** on-page search box (AD-19, AD-22).
UX-DR4 (docs-sidebar styling): current topic marked with fuchsia (`#d946ef`) text + a left accent bar, ancestors bolded; sidebar width ~300px; sections expand/collapse with current page auto-expanded on load; mobile = Hextra drawer (hamburger); keyboard-navigable.
UX-DR5 (theme-aware accent tokens — build requirement): the bright stub-badge and Go Deeper label tints pass AA on dark surfaces but fail on light-mode white; ship them as theme-aware pairs with darker light-mode companions (`#6d28d9` violet, `#a21caf` fuchsia, `#b45309` amber, all AA on white) in `assets/css/custom.css`; verify in the light theme before launch.
UX-DR6 (Pro Tip callout): convention for Carrie's first-person "Pro Tip" asides using a Hextra info/tip callout styled with the fuchsia/amber accent family (not generic blue); non-interactive emphasis.
UX-DR7 (Topic layout tuning): content column at reading-max ~760px; topic H1 at 2.0rem, body 1.05rem / line-height ~1.6; breadcrumbs `Handbook / {Section} / {Topic}` above the title *(Hextra default — verify)*; on-this-page TOC auto-built from H2/H3, hidden when <2 headings, scroll-synced *(Hextra default)*; prev/next nav at bottom of topic *(Hextra default)*.
UX-DR8 (Accessibility floor — WCAG 2.1 AA): a skip-to-content link past the sidebar; all interactive elements are real `<a>`/`<button>` with accessible labels and a visible focus ring (`2px solid #d946ef`, `:focus-visible`); sidebar tree, TOC, search, and in-page links keyboard-reachable/operable with logical tab order; one `<h1>` per page + logical H2/H3 order; landmark regions (`header`/`nav`/`main`/`aside`/`footer`); honor `prefers-reduced-motion` (drop hover lifts and TOC/scroll animation); descriptive external link text (never "click here").
UX-DR9 (Voice & microcopy conventions): Section/Topic framing intros that orient fast ("what this is / why it matters"); honest, warm stub microcopy (not apologetic boilerplate); Go Deeper items carry a one-line opinion, not just a link; emoji sparing and friendly (🚧 on stubs, 📚 on Go Deeper).

### FR Coverage Map

FR-1: Epic 1 — `/handbook` section reachable + "Handbook" header entry
FR-2: Epic 1 — 12-Section sidebar tree, fixed order/slugs
FR-7: Epic 1 — Full-text search scoped to the Handbook
FR-11: Epic 1 — Brand consistency with carriekroutil.com
FR-12: Epic 1 + Epic 3 — Pipeline-ready / `handbook` branch builds green (1); go-live merge to `main` (3)
FR-4: Epic 2 — Consistent topic anatomy + last-updated
FR-5: Epic 2 — Go Deeper block on every topic (incl. stubs)
FR-6: Epic 2 — Stub treatment ("🚧 Expanding")
FR-9: Epic 2 — Migrate substantial wiki pages into the IA
FR-10: Epic 2 — Complete IA with stubs at go-live
FR-3: Epic 3 — Start Here guided path
FR-13: Epic 3 — GoatCounter + sitemap/crawl, verified in production

*(FR-8 retired — tags deferred.)*

**NFR / AD anchoring (cross-cutting):**
- NFR-1 (accessibility): Epic 1 (shell a11y floor: skip-link, focus, landmarks, keyboard) + Epic 2 (per-topic semantics, alt text)
- NFR-2 (performance): Epic 1 (static, Lighthouse ≥ 90, no heavy JS)
- NFR-3 (SEO/discoverability): Epic 1 (clean URLs) + Epic 2 (per-topic title/description) + Epic 3 (sitemap/crawl verify)
- NFR-4 (privacy): Epic 1 (preserve inherited cookieless analytics) + Epic 3 (verify prod-only)
- NFR-5 (maintainability): Epic 2 (markdown + front-matter authoring; Go Deeper convention)
- AD-14, AD-16 (section weights), AD-21, inherited envelope + branch discipline, no-new-dependency stack: Epic 1
- AD-15, AD-17, AD-18, AD-9, AD-20, AD-22 (cross-topic links): Epic 2
- AD-19, AD-22 (guided-path links): Epic 3
- UX-DR4, UX-DR7 (verify), UX-DR8 (shell floor): Epic 1 · UX-DR1, UX-DR2, UX-DR5, UX-DR6, UX-DR7, UX-DR9: Epic 2 · UX-DR3 + welcome copy: Epic 3

## Epic List

All Epic 1–3 work lands on a single **`handbook`** feature branch in `carriekroutil-site` (per AD-20 + inherited branch discipline); the only `main` merge is the go-live PR at the very end of Epic 3. Dependency flow: **1 → 2 → 3**, each standalone and building on prior epics without requiring a later one. *(Execution autonomy preference: Epics 1–2 run autonomously to a PR; Epic 3 is worked story-by-story.)*

### Epic 1: A branded, navigable Handbook shell
A visitor can reach `/handbook` from a "Handbook" header entry, browse the full 12-Section sidebar tree (fixed order/slugs, with `linkTitle: "The Transition"` short-labeling section 1), land on section indexes, and search the Handbook — all visually continuous with carriekroutil.com and building green on the `handbook` branch, ready to ship through the existing Amplify pipeline.
**FRs covered:** FR-1, FR-2, FR-7, FR-11, FR-12 (pipeline-ready; branch builds green)
*Key ADs/UX-DRs:* AD-14, AD-16 (section weights), AD-21, inherited envelope + branch discipline; UX-DR4, UX-DR8 (shell a11y floor), UX-DR7 (verify Hextra defaults). NFR-2/3/4. Guardrail: do not exclude `/handbook` from inherited analytics/sitemap so FR-13 verifies in Epic 3. Creates the 12 Section `_index.md` scaffolds + `/handbook/_index.md` with `cascade:{type:docs}` (Start Here body/layout authored in Epic 3).

### Epic 2: Topic content model + the complete IA
Every topic renders with the same anatomy (title → framing intro → body → Go Deeper, with "last updated"), a curated Go Deeper block, and — when unfinished — an honest "🚧 Expanding" badge; and the full Section/Topic tree is populated: substantial wiki pages migrated into their mapped sections, every remaining v1 topic shipped as a Stub. Nothing 404s; everything is searchable (realizes UJ-1, UJ-3).
**FRs covered:** FR-4, FR-5, FR-6, FR-9, FR-10
*Key ADs/UX-DRs:* AD-15, AD-17, AD-18, AD-9, AD-20, AD-22 (cross-topic links); NFR-1, NFR-5, NFR-3 (per-topic meta); UX-DR1, UX-DR2, UX-DR5, UX-DR6, UX-DR7, UX-DR9.
*Ordered stories:* build conventions (anatomy, `go-deeper.html`, `stub-badge.html`, Pro Tip callout, theme-aware accent tokens) → migrate the M-topics per the addendum table → author the S-stubs (including the new *Managing Yourself → Building your leadership network*) → completeness check (no orphans, all searchable).

### Epic 3: The Start Here front door + Go-Live
A new manager lands on Start Here and follows the ordered, annotated guided path (with an "experienced? jump to reference/search" escape and the "Written and curated by Carrie Kroutil…" About nudge); then Carrie validates the complete handbook and it goes live on carriekroutil.com.
**FRs covered:** FR-3, FR-13; completes FR-12 (go-live merge)
*Key ADs/UX-DRs:* AD-19, AD-22 (guided-path links via relref); UX-DR3 + the approved welcome copy; NFR-3 (sitemap/crawl verified in prod). Comes after Epic 2 so its `relref` path-cards resolve (AD-22 build-failing).
*Ordered stories:* (1) Build Start Here — welcome copy + numbered path-step cards + section grid + About nudge. (2) UAT by Carrie — review the full handbook on the `handbook` branch (local `hugo server`); log issues; fix loop until sign-off. (3) Go-live PR (very last step) — merge `handbook` → `main`; Amplify deploys to `carriekroutil.com/handbook`. (4) Verify analytics & discoverability in prod (FR-13) — GoatCounter records `/handbook` views (cookieless, prod-only); pages in sitemap + crawlable.

## Epic 1: A branded, navigable Handbook shell

A visitor can reach `/handbook` from a "Handbook" header entry, browse the full 12-Section sidebar tree in fixed order, land on section indexes, and search the Handbook — all visually continuous with carriekroutil.com and building green on the `handbook` branch, ready to ship through the existing Amplify pipeline. All Epic 1 work lands on the `handbook` feature branch in `carriekroutil-site` (never `main`).

### Story 1.1: Stand up the /handbook docs-mode section + header nav

As a visitor to carriekroutil.com,
I want a "Handbook" entry in the site header that opens a `/handbook` area rendered in docs mode,
So that I can find and enter the Handbook as a natural part of the site.

**Acceptance Criteria:**

**Given** any page of carriekroutil.com
**When** I look at the header nav
**Then** I see a "Handbook" entry positioned between "Posts" and "About" (menu `weight = 15`)
**And** activating it loads `/handbook`.

**Given** the `/handbook` route
**When** it renders
**Then** it uses Hextra docs mode (a persistent left sidebar tree is present) because `content/handbook/_index.md` sets `cascade: { type: docs }`
**And** Posts, Home, and About keep their existing layouts.

**Given** the rest of the site (Posts, About, Projects, Experience, search, theme toggle)
**When** I use them after the change
**Then** they all work unchanged.

**Given** a local build
**When** I run `hugo server`
**Then** `/handbook` builds without errors.

### Story 1.2: Scaffold the 12 Sections in fixed order with stable slugs

As a Handbook visitor,
I want the sidebar to list all 12 Sections in the curated fixed order,
So that I can navigate the whole handbook structure.

**Acceptance Criteria:**

**Given** the Handbook sidebar
**When** it renders
**Then** it lists exactly these 12 Sections top-to-bottom: The Transition · Managing Yourself · People & Leadership · Performance & Growth · Hiring & Team Building · Team Health & Operations · Delivery & Execution · Operational Excellence · Tools & Productivity · Building with AI · Security & Governance · Engineering Foundations.

**Given** each Section folder
**Then** its slug matches the fixed AD-21 map (`the-transition` … `engineering-foundations`)
**And** its `_index.md` sets a unique `weight` 1..12 in that order.

**Given** section 1
**When** it is shown in the sidebar
**Then** it displays the short label "The Transition" (via `linkTitle`)
**And** its section page title remains "The Transition (Engineer ⇄ Manager)".

**Given** each Section index page
**Then** it renders a one-paragraph "what this section covers" intro (placeholder acceptable at this stage)
**And** it is reachable from the sidebar (no orphan pages).

### Story 1.3: Brand-match the docs-mode shell (sidebar active state + tokens)

As a visitor moving between the blog and the Handbook,
I want the Handbook to look like the same site,
So that it feels like one cohesive brand.

**Acceptance Criteria:**

**Given** a Handbook page
**When** it renders
**Then** it uses the same theme, fuchsia-primary tokens, dark-default + toggle, header, and footer as the rest of carriekroutil.com
**And** no new color values are introduced (overrides live only in `assets/css/custom.css`).

**Given** the sidebar
**When** I am on a section or topic
**Then** the current item is marked with the fuchsia link color + a left accent bar and its ancestors are bolded
**And** the sidebar width is ~300px.

**Given** the change
**Then** no bespoke or off-brand UI is introduced — only docs-mode chrome (sidebar/TOC) styled to brand.

### Story 1.4: Full-text search across the Handbook

As a reader,
I want to find a Handbook page by keyword from the site search,
So that I can jump straight to what I need.

**Acceptance Criteria:**

**Given** the site search (header ⌕ / ⌘K)
**When** I search a term present in a Handbook page's title, headings, or body
**Then** that page is returned in the results.

**Given** Hextra FlexSearch configuration (`index=content`, `maxSectionResults=1`)
**When** a single page matches
**Then** it returns as one result, not one result per heading.

**Given** a search result
**When** I select it
**Then** it deep-links into the corresponding `/handbook/...` page.

*(Note: fully exercised once topics exist in Epic 2; here verified against the section index pages.)*

### Story 1.5: Accessibility & semantic floor for the shell

As a keyboard or screen-reader user,
I want the Handbook shell to be navigable and well-structured,
So that I can use it without barriers (and the site practices the accessibility it preaches).

**Acceptance Criteria:**

**Given** any Handbook page
**When** I navigate by keyboard
**Then** a "skip to content" link lets me bypass the sidebar
**And** the sidebar tree, search, theme toggle, and in-page links are reachable and operable with a logical tab order and a visible focus ring (`2px` solid fuchsia, `:focus-visible`).

**Given** the page structure
**Then** landmark regions (`header`/`nav`/`main`/`aside`/`footer`) are present
**And** there is exactly one `<h1>` per page.

**Given** the user has `prefers-reduced-motion` set
**When** the page renders
**Then** hover lifts and TOC/scroll animations are dropped.

### Story 1.6: Pipeline-ready build on the handbook branch

As Carrie,
I want the Handbook to build green on the `handbook` branch and be ready to ship through the existing pipeline,
So that go-live is just a merge with no infra surprises.

**Acceptance Criteria:**

**Given** all Handbook work is on the `handbook` branch (never `main`)
**When** I run `hugo --gc --minify`
**Then** the build succeeds with no errors
**And** no new dependency is added (`HUGO_VERSION` and `go.mod` are unchanged).

**Given** the deploy envelope
**Then** no new pipeline, branch preview, or environment is introduced (inherited single Amplify pipeline, `main`-only, previews off).

**Given** the discoverability and performance guardrails
**Then** `/handbook/{section}/` URLs are clean and human-readable
**And** the Handbook is not excluded from the inherited sitemap or analytics
**And** pages carry no heavy client JS beyond Hextra defaults (search is the one allowed JS exception).

## Epic 2: Topic content model + the complete IA

Every topic renders with the same anatomy (title → framing intro → body → Go Deeper, with "last updated"), a curated Go Deeper block, and — when unfinished — an honest "🚧 Expanding" badge; and the full Section/Topic tree is populated: substantial wiki pages migrated into their mapped sections, every remaining v1 topic shipped as a Stub. Nothing 404s; everything is searchable (realizes UJ-1, UJ-3). All work continues on the `handbook` branch.

*Story shape: stories 2.1–2.3 build the reusable conventions every topic depends on; 2.4–2.18 populate the IA one Section at a time (heavy sections split across stories per single-dev-agent sizing); 2.19 verifies completeness and wires cross-topic links. Per-topic markdown lives solely in `carriekroutil-site/content/handbook/` (AD-20).*

**Shared content-story acceptance pattern** (applies to every topic created in 2.4–2.18): each **migrated (M)** topic ports its wiki content into the new IA with headings adapted, complete front-matter (AD-15), preserved/flagged external links, and alt text on any image (AD-9); each **stub (S)** topic ships `stub: true`, a warm honest intro, and a Go Deeper block; **every** topic has a unique `weight` within its Section, is reachable from the sidebar (no orphan), is searchable, and carries a non-empty Go Deeper block (build-fails otherwise, AD-17).

### Story 2.1: Topic anatomy + front-matter contract (+ Pro Tip callout)

As an author and reader,
I want every topic to share one anatomy and front-matter contract,
So that the handbook feels coherent and authoring stays plain markdown.

**Acceptance Criteria:**

**Given** any topic
**When** it renders
**Then** it shows title → framing intro → body → Go Deeper in that order
**And** a "Last updated" date is shown from the authored `lastUpdated` field.

**Given** a topic file
**Then** its YAML front matter carries `title`, a unique-per-Section `weight`, `description`, optional `stub`, `goDeeper`, and `lastUpdated`
**And** the file is flat `content/handbook/{section}/{topic}.md` (a page bundle `{topic}/index.md` is used only for image-bearing topics, where alt text is build-failing per AD-9).

**Given** a topic with two or more headings
**Then** the auto "On this page" TOC renders, and breadcrumbs and prev/next render (Hextra defaults verified)
**And** the content column is ~760px wide with H1 at 2.0rem.

**Given** a first-person aside authored as a Pro Tip
**When** it renders
**Then** it uses a Hextra callout styled with the fuchsia/amber family (not generic blue).

### Story 2.2: Go Deeper block partial + theme-aware accent tokens

As a reader,
I want curated next steps at the end of every topic,
So that I always get a vetted path forward.

**Acceptance Criteria:**

**Given** a topic's `goDeeper` list of `{group, title, url, why}` items (`group ∈ Books · Courses · Tools`)
**When** rendered by the `go-deeper.html` partial
**Then** a "📚 Go Deeper" card shows only the populated groups, each item a body-color title link (fuchsia on hover) plus a one-line "why"
**And** external links open in a new tab with `rel="noopener"`.

**Given** a topic — including a stub — with no `goDeeper` items
**When** the site builds
**Then** the build fails.

**Given** the group labels and the active theme
**Then** Books/Courses/Tools tints use the existing `--ck-chip-*` tokens
**And** theme-aware light-mode companions are used (verified AA on white).

### Story 2.3: Stub treatment + microcopy

As a reader landing on an unfinished topic,
I want honest framing and real value,
So that the visit isn't wasted.

**Acceptance Criteria:**

**Given** a topic with `stub: true`
**When** it renders
**Then** an inline non-heading "🚧 Expanding" amber badge appears beside the title (theme-aware text, never red, not a link)
**And** it does not disturb heading order or the auto-TOC.

**Given** a stub
**Then** it ships a warm, honest intro (not boilerplate, not repeating the badge label) plus a full Go Deeper block
**And** it is fully navigable and searchable — never a 404, never empty, never `draft: true`.

### Story 2.4: Populate The Transition (§1)

As a new or transitioning manager,
I want The Transition section populated,
So that I can navigate the identity-shift topics from day one.

**Acceptance Criteria:**

**Given** Section 1, **Then** these topics exist as stubs (intro + Go Deeper): Should you make the move? · The Engineer/Manager pendulum · Your first 90 days · Mindset shifts & imposter syndrome · Going back to IC: the swing back.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern (front-matter, badge, Go Deeper, unique weight, reachable, searchable).

### Story 2.5: Populate Managing Yourself (§2)

As a manager sustaining myself,
I want Managing Yourself populated,
So that I can find self-management guidance and curated next steps.

**Acceptance Criteria:**

**Given** Section 2, **Then** these topics exist: Time & energy management (M, from 009 Tips) · Staying organized: email, brag list (M, from 009 Tips) · Boundaries & avoiding burnout (S) · Growing yourself as a leader (S) · Building your leadership network (S).
**Given** the migrated topics, **Then** the relevant 009 Tips content (time/energy, email, brag list) is ported here per the split, edited to the new IA.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.6: Populate People & Leadership — leadership, inclusion, belonging (§3, part A)

As a people leader,
I want the core People & Leadership topics migrated,
So that the substantial existing guidance is available in the new IA.

**Acceptance Criteria:**

**Given** Section 3, **Then** these migrated topics exist: What an engineering leader does (M, 001) · Inclusive teams (M, 003) · Culture of belonging (M, 004).
**Given** 003 Inclusive Teams, **Then** only its people-leadership portion lands here; its interviewing portion is reserved for §5 (Story 2.9).
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.7: Populate People & Leadership — communication & strategy (§3, part B)

As a people leader,
I want the communication and strategy topics migrated,
So that the section is complete without overloading a single work unit.

**Acceptance Criteria:**

**Given** Section 3, **Then** these migrated topics exist: Communicating effectively (M, 006) · Acting & thinking strategically (M, 005).
**Given** each topic, **Then** it follows the shared content-story acceptance pattern
**And** unique weights are coordinated with Story 2.6 so the five §3 topics order correctly.

### Story 2.8: Populate Performance & Growth (§4)

As a manager growing my people,
I want Performance & Growth populated,
So that I can reach coaching, feedback, and review guidance.

**Acceptance Criteria:**

**Given** Section 4, **Then** these topics exist: Running great 1:1s (S) · Coaching skills (M, 002) · Giving feedback (S) · Performance reviews & calibration (S) · Handling underperformance (S) · Promotions & career ladders (S).
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.9: Populate Hiring & Team Building (§5)

As a manager building a team,
I want Hiring & Team Building populated,
So that hiring guidance (including inclusive interviewing) is reachable.

**Acceptance Criteria:**

**Given** Section 5, **Then** these topics exist: Inclusive interviewing (M-partial, from 003) · Resume screening without bias (S) · Onboarding (S) · Building a team (S).
**Given** the inclusive-interviewing topic, **Then** the interviewing portion of 003 is ported here (complementing the §3 people-leadership portion in Story 2.6) without duplication.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.10: Populate Team Health & Operations (§6)

As a manager running a healthy team,
I want Team Health & Operations populated,
So that team-health, ceremonies, and lifecycle guidance is reachable.

**Acceptance Criteria:**

**Given** Section 6, **Then** these topics exist: Team health (M, 007 — thin) · Team ceremonies (M, 011 — thin) · Development lifecycle (M, 012).
**Given** 012 Development Lifecycle, **Then** its team-health/operations portion lands here; its delivery portion is reserved for §7 (Story 2.11), split rather than duplicated.
**Given** the thin pages (007, 011), **Then** existing content is ported and rounded out with a Go Deeper block (marked `stub: true` if still thin).

### Story 2.11: Populate Delivery & Execution (§7)

As a manager delivering work,
I want Delivery & Execution populated,
So that the levers, delegation, and prioritization guidance is reachable.

**Acceptance Criteria:**

**Given** Section 7, **Then** these topics exist: Scope, resources & time: the levers (M, from 009 Tips) · Delegation (M, from 009 Tips) · Prioritization & roadmaps (S) · Tech debt vs. business impact (S).
**Given** the migrated topics, **Then** the levers/delegation content from 009 Tips and the delivery portion of 012 are ported here per the split.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.12: Populate Operational Excellence (§8)

As a manager running operations,
I want Operational Excellence populated,
So that on-call, monitoring, and incident topics exist from launch.

**Acceptance Criteria:**

**Given** Section 8, **Then** these topics exist as stubs: On-call · Monitoring & alerting · Incident response.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.13: Populate Tools & Productivity (§9)

As a hands-on manager,
I want Tools & Productivity populated,
So that automation tools and everyday tips are reachable.

**Acceptance Criteria:**

**Given** Section 9, **Then** these migrated topics exist: Automation tools (M, 008) · Everyday tips & tricks (M, 009 — Chrome search, Slack, VS Code, git) · Training & content-creation tools (M, from 009 Tips).
**Given** 009 Tips & Tricks, **Then** only its tooling/tips portions land here (the brag-list/email/time portion went to §2; the levers/delegation portion to §7) — split, not duplicated.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.14: Populate Building with AI (§10)

As a manager leading in the AI era,
I want Building with AI populated,
So that AI-literacy topics exist from launch.

**Acceptance Criteria:**

**Given** Section 10, **Then** these topics exist as stubs: AI literacy for EMs · AI-assisted engineering · AI-native engineering · Leading AI-adopting teams.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.15: Populate Security & Governance (§11)

As a manager who needs security & compliance literacy,
I want Security & Governance populated,
So that standards-org and compliance topics are reachable.

**Acceptance Criteria:**

**Given** Section 11, **Then** these topics exist: Security standards & orgs: OWASP, NIST (M, 014 — thin) · Compliance literacy: SOC 2, ISO 27001, GDPR, CCPA & cookie compliance (S).
**Given** the Compliance literacy topic, **Then** its scope explicitly covers cookie compliance — aligning website tracking technologies (cookies) with privacy laws (GDPR, CCPA) — and may cross-reference the handbook's own cookieless GoatCounter choice.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.16: Populate Engineering Foundations — architecture, metrics & agile (§12, part A)

As an engineer demystifying the must-knows,
I want the architecture and delivery-metrics foundations migrated from 013,
So that these technical topics each have their own page.

**Acceptance Criteria:**

**Given** Section 12, **Then** these migrated topics exist (from 013 Technical Upskilling): Software architecture & design (Architecture Styles, 12-Factor App, SOLID, Design Patterns) · Delivery metrics & agile foundations (DORA Metrics, Agile Manifesto).
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.17: Populate Engineering Foundations — cloud-native, networking & learning path (§12, part B)

As an engineer demystifying the must-knows,
I want the cloud-native, networking, and learning-path foundations migrated from 013,
So that the remaining 013 content has its own pages.

**Acceptance Criteria:**

**Given** Section 12, **Then** these migrated topics exist (from 013): Cloud-native & infrastructure (CNCF, Docker, Kubernetes, Terraform) · Networking & protocols (HTTP, communication protocols) · A software-engineering learning path (Julia Evans/Wizardzines, notable figures, the curated book list & learning path).
**Given** unique weights, **Then** they are coordinated with Story 2.16 and 2.18 so the §12 topics order correctly.
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.18: Populate Engineering Foundations — the must-know stubs (§12, part C)

As a bootcamp or self-taught engineer,
I want the foundational must-know stubs present,
So that i18n, a11y, video localization, and debugging are named and curated from launch.

**Acceptance Criteria:**

**Given** Section 12, **Then** these topics exist as stubs: Internationalization (i18n) · Accessibility (a11y) · Video localization: captions + spoken-word · Debugging craft.
**Given** the Debugging craft topic, **Then** its Go Deeper may cross-reference Julia Evans/Wizardzines from the learning-path topic (Story 2.17).
**Given** each topic, **Then** it follows the shared content-story acceptance pattern.

### Story 2.19: Completeness check + cross-topic links

As Carrie,
I want a final pass confirming the IA is complete and internally linked,
So that go-live keeps its "complete IA, nothing 404s" promise.

**Acceptance Criteria:**

**Given** the full Section/Topic tree
**Then** every Section has all its v1 topics (Migrated or Stub), there are no orphan pages, and nothing 404s
**And** every topic is searchable and carries a non-empty Go Deeper block.

**Given** the net-new Sections (The Transition, Managing Yourself, Hiring & Team Building, Delivery & Execution, Operational Excellence, Building with AI) and the new must-knows (i18n, a11y, video localization)
**Then** all are present at least as stubs with Go Deeper.

**Given** cross-topic references in topic bodies
**When** authored
**Then** they use Hugo `ref`/`relref` (build-failing on a missing target), now resolvable because all topics exist.

## Epic 3: The Start Here front door + Go-Live

A new manager lands on Start Here and follows the ordered, annotated guided path (with an "experienced? jump to reference/search" escape and the "Written and curated by Carrie Kroutil…" About nudge); then Carrie validates the complete handbook and it goes live on carriekroutil.com. Comes after Epic 2 so the guided-path `relref` links resolve (AD-22 build-failing). Ends with the single `handbook` → `main` merge = go-live.

### Story 3.1: Build the Start Here landing (guided path)

As a new manager,
I want a Start Here landing with an ordered guided path,
So that I get oriented without a firehose.

**Acceptance Criteria:**

**Given** `/handbook` (root `_index.md`)
**When** it renders
**Then** a root-scoped `layouts/docs/list.html` override shows the approved welcome copy → numbered path-step cards → a section grid → the inherited footer, in a single centered column (sidebar still present; the three-column docs frame deliberately broken). *(The override lives at the docs `list.html` slot, not `layouts/handbook/list.html`: `cascade type:docs` routes handbook section-list pages to the docs type slot, which outranks the section slot in Hugo's lookup — so it must guard on the root path and delegate the 12 Section indexes to the theme default.)*

**Given** the layout override
**Then** it is scoped to the `/handbook/` root index only
**And** the 12 Section `_index.md` pages keep Hextra's default docs section-list.

**Given** the guided path
**Then** the path-step cards are ordered The Transition → Managing Yourself → People & Leadership → Performance & Growth → Hiring & Team Building, each with a one-line "why"
**And** each card links via Hugo `relref` (build-failing if a target is missing).

**Given** a non-newcomer
**Then** an explicit "experienced? jump to the reference or search" affordance is present
**And** there is no on-page search box (the header ⌘K already covers Handbook pages).

**Given** the About nudge
**Then** it reads "Written and curated by Carrie Kroutil — […]. More about me →" and links to `/about`.

**Given** the build
**When** `hugo` runs
**Then** it builds green with all `relref` targets resolving.

### Story 3.2: UAT by Carrie (acceptance review + fix loop)

As Carrie,
I want to review the complete handbook on the branch and have issues fixed,
So that I sign off before go-live.

**Acceptance Criteria:**

**Given** the `handbook` branch
**When** I run `hugo server` locally
**Then** I can review the full handbook (all sections/topics, Start Here, search, stubs, Go Deeper, brand, accessibility) — Amplify previews being disabled.

**Given** issues I log during UAT
**When** they are triaged
**Then** they are addressed on the `handbook` branch and re-verified, looping until I sign off.

**Given** my sign-off
**Then** the branch is acceptance-complete and ready for the go-live PR.

### Story 3.3: Go-live PR — merge handbook to main (very last step)

As Carrie,
I want a PR to merge `handbook` into `main`,
So that the handbook deploys live.

**Acceptance Criteria:**

**Given** UAT sign-off
**When** I open a PR from `handbook` → `main` in `carriekroutil-site`
**Then** it bundles the complete handbook work.

**Given** the PR is merged to `main`
**Then** the single inherited Amplify pipeline builds and deploys to `carriekroutil.com/handbook`
**And** no second pipeline, repo, or domain is introduced.

**Given** go-live
**Then** this is the only `main` merge, and nothing was deployed before it.

### Story 3.4: Verify analytics & discoverability in production

As Carrie,
I want to confirm analytics and crawlability post-launch,
So that I can measure traffic and search engines index the handbook.

**Acceptance Criteria:**

**Given** the deployed handbook
**When** pages are viewed in production
**Then** GoatCounter records `/handbook` page views (cookieless, no PII, production only).

**Given** discoverability
**Then** handbook pages appear in the sitemap and are crawlable (indexable robots policy).

**Given** privacy
**Then** no consent banner is introduced.
