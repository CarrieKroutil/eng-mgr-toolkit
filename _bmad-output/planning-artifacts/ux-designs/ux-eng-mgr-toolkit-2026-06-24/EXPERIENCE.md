---
status: final
created: 2026-06-24
updated: 2026-06-25
sources:
  - ../../prds/prd-eng-mgr-toolkit-2026-06-24/prd.md
  - ../../briefs/brief-eng-mgr-toolkit-2026-06-24/brief.md
  - ../../briefs/brief-eng-mgr-toolkit-2026-06-24/addendum.md
  - ./DESIGN.md
inherits:
  - carriekroutil.com EXPERIENCE.md (Hextra docs-mode delta only)
mockups:
  - ./mockups/start-here.html
  - ./mockups/topic.html
---

# Engineering Manager Handbook — Experience

## Foundation

- **Form factor:** responsive website, a documentation section added to carriekroutil.com. Desktop = three-column docs (sidebar · content · TOC); mobile = single column with drawer sidebar.
- **Build system:** static site, **Hugo + Hextra** in **docs mode**, shipped through the existing AWS Amplify pipeline in the `carriekroutil-site` repo (`content/handbook/`). No app, no accounts, no server. Behavior not specified here inherits Hextra's docs defaults; this doc records only the *delta*.
- **Visual identity:** see `DESIGN.md`, which inherits carriekroutil.com's `DESIGN.md`. Tokens referenced by name (`{colors.link}`, `{spacing.sidebar-width}`). Spines win over any mock or theme default.
- **Content model:** every Topic is a markdown file under `content/handbook/{section}/{topic}.md` with front matter: `title`, `weight` (sidebar order), `description` (meta/SEO), `lastUpdated` (or git-derived; rendered under the Topic title via Hextra's built-in last-updated), and `stub: true|false`. Section order is fixed via `weight` on `_index.md` files. Publishing = write markdown → push → Amplify builds → live. Never a CMS.
- **Authoring conventions:** every Topic ends with a **Go Deeper** block; stubs set `stub: true` (drives the badge) and still ship a framing intro + Go Deeper. First-person "Pro Tip" asides use the pro-tip callout.

## Information Architecture

**Front door + reference, one tree.** `/handbook` is the **Start Here** landing page (the guided path); the persistent sidebar is the **topic reference** (12 Sections). Both always reachable.

**Global nav delta:** add a **Handbook** entry to the existing sticky header, placed between Posts and About (`hugo.toml` `weight = 15`) — `Posts · Handbook · About · ⌕ · ☾ · GitHub · LinkedIn`. Label is **"Handbook"** (clean global-nav register matching the `/handbook/` URL; the full "Engineering Manager Handbook" identity lives in the page H1, `<title>`, and share metadata). Not "EM Wiki" — "Wiki" contradicts the curated-handbook positioning we're migrating *toward*. *(Reconciled 2026-06-25 to match Architecture AD-14, now the definitive source for the nav label across PRD/UX/Architecture.)*

### Surfaces

1. **Start Here `/handbook/`** — landing page: short welcome → the **guided path** (ordered path-step cards) → a "browse all 12 sections" grid → the inherited site footer. Serves newcomers (the path) and everyone else (the section grid) from one door. **No on-page search box or jump button** — the header search (⌘K) already covers Handbook pages, and the sidebar always lists all 12 Sections. Realizes UJ-2.
2. **Section index `/handbook/{section}/`** — a Section's landing: one-paragraph "what this section covers" + a list/grid of its Topics. (Hextra auto-generates; we add the intro.)
3. **Topic `/handbook/{section}/{topic}/`** — the unit of content (see **Topic anatomy** below). Realizes UJ-1, UJ-3.
4. **Search** — Hextra's built-in full-text search (header ⌕ / ⌘K), scoped so Handbook topics are findable; one result per topic. Realizes UJ-1, UJ-3.
5. Inherited site surfaces (Home, Posts, About, Tags, 404) are unchanged.

**URLs** — clean, hierarchical, one page each: `/handbook/` → `/handbook/{section}/` → `/handbook/{section}/{topic}/`; the on-page TOC adds a `#heading` anchor. Multi-page static, **not a SPA** — every Topic is independently linkable, bookmarkable, and search-indexable. This per-Topic permalink is what makes UJ-3 (Sam deep-links from a search engine) land.

### Section → Topic map (v1)

The 12 Sections in fixed order, with their v1 Topics. **M** = migrated from wiki (per addendum table), **S** = stub at launch (intro + Go Deeper, expand in place). `[ASSUMPTION]` this topic breakdown is a proposed starting structure — confirm/adjust.

1. **The Transition (Engineer ⇄ Manager)** — Should you make the move? (S) · The Engineer/Manager Pendulum (S) · Your first 90 days (S) · Mindset shifts & imposter syndrome (S) · Going back to IC: the swing back (S)
2. **Managing Yourself** — Time & energy management (M, from Tips) · Staying organized: email, brag list (M, Tips) · Boundaries & avoiding burnout (S) · Growing yourself as a leader (S) · Building your leadership network (S)
3. **People & Leadership** — What an engineering leader does (M, 001) · Inclusive teams (M, 003) · Culture of belonging (M, 004) · Communicating effectively (M, 006) · Acting & thinking strategically (M, 005)
4. **Performance & Growth** — Running great 1:1s (S) · Coaching skills (M, 002) · Giving feedback (S) · Performance reviews & calibration (S) · Handling underperformance (S) · Promotions & career ladders (S)
5. **Hiring & Team Building** — Inclusive interviewing (M-partial, 003) · Resume screening without bias (S) · Onboarding (S) · Building a team (S)
6. **Team Health & Operations** — Team health (M, 007 — thin) · Team ceremonies (M, 011 — thin) · Development lifecycle (M, 012)
7. **Delivery & Execution** — Scope, resources & time: the levers (M, Tips) · Delegation (M, Tips) · Prioritization & roadmaps (S) · Tech debt vs. business impact (S)
8. **Operational Excellence** — On-call (S) · Monitoring & alerting (S) · Incident response (S)
9. **Tools & Productivity** — Automation tools (M, 008) · Everyday tips & tricks (M, 009 — Chrome search, Slack, VS Code, git) · Training & content-creation tools (M, Tips)
10. **Building with AI** — AI literacy for EMs (S) · AI-assisted engineering (S) · AI-native engineering (S) · Leading AI-adopting teams (S)
11. **Security & Governance** — Security standards & orgs: OWASP, NIST (M, 014 — thin) · Compliance literacy: SOC 2, ISO 27001, GDPR, CCPA & cookie compliance (S)
12. **Engineering Foundations** *(last — deepest)* — Software architecture & design (M, 013) · Delivery metrics & agile foundations (M, 013) · Cloud-native & infrastructure (M, 013) · Networking & protocols (M, 013) · A software-engineering learning path (M, 013) · Internationalization (i18n) (S) · Accessibility (a11y) (S) · Video localization: captions + spoken-word (S) · Debugging craft (S) *(013 Technical Upskilling split into the five M-topics above)*

**Per-section nesting:** flat by default (Section → Topics, one level). Only **Engineering Foundations** and **Performance & Growth** are large enough to *optionally* sub-group later; not needed for v1. Long Topics stay single pages — the auto-TOC handles in-page navigation (no splitting unless a sub-part deserves its own URL/search hit).

**Visual reference:** [Start Here](./mockups/start-here.html), [Topic page — full + stub states](./mockups/topic.html). Section index and search derive from Hextra defaults + this IA (spine-only). Spines win on conflict.

**Closure check:** every PRD need has a surface — guided onboarding → Start Here path; quick lookup → sidebar + search + Topic; "demystify the must-knows" → Foundations + Operational Excellence + Building with AI topics; curation → Go Deeper on every Topic. Every surface has a journey landing on it (see Key Flows). IA closes.

## Voice and Tone

Practical, warm, direct, **opinionated** — Carrie talking to a manager she's coaching. Consistent with the blog's playful voice but a notch more instructive (it's a handbook). Curated *with judgment*: say what's worth your time and why; never a neutral link dump.

- **Section/Topic intros** frame fast: "Here's what this is and why it matters," then get practical.
- **Pro Tips** are first-person asides in a callout: "> **Pro Tip:** Don't use email as a stressful to-do list."
- **Go Deeper** items carry a one-line opinion, not just a link: "*High Output Management* — the one book to read first; dense but worth it."
- **Stub microcopy** is honest and warm, never apologetic boilerplate: "🚧 **Expanding** — the deep-dive's still cooking. Meanwhile, the Go Deeper picks below are the real ones I'd hand you."
- Emoji: sparing and friendly (a 🚧 on stubs, 📚 on Go Deeper) — matching the blog's restraint.

## Component Patterns (behavioral)

Visual specs in `DESIGN.md.Components`; behavior here.

- **docs-sidebar** — persistent left tree; Sections expand/collapse, current Topic marked (fuchsia + accent bar), ancestors bolded. Keyboard-navigable. On mobile = Hextra drawer (hamburger).
- **on-this-page-toc** — right rail, auto-built from the Topic's H2/H3, scroll-synced; clicking jumps to the heading. Hidden when a Topic has <2 headings. On mobile = collapsible "On this page" at top.
- **breadcrumbs** — `Handbook / {Section} / {Topic}`; each crumb links up.
- **go-deeper-block** — closes every Topic; renders Books/Courses/Tools groups that are populated; each item links out (external links open in a new tab with rel noopener). Present even on stubs.
- **stub-badge** — shown when `stub: true`; purely informational (not a link).
- **path-step-card** (Start Here) — entire card links to the Topic; shows step number, name, one-line why.
- **section-grid-card** (Start Here) — links to the Section index.
- **search** — header ⌕ / ⌘K opens Hextra full-text search; type-to-filter; keyboard-navigable; one result per Topic; results deep-link into `/handbook/...`.
- **prev-next-nav** — bottom-of-Topic prev/next (within guided path order on path topics; within Section otherwise). Hextra default.
- **pro-tip-callout** — non-interactive Hextra callout; just emphasis.

## State Patterns

- **Default:** dark theme (inherited); theme choice persists (Hextra).
- **Stub state:** `stub: true` → amber "🚧 Expanding" badge by the title + honest intro microcopy + full Go Deeper. Fully navigable and **searchable** — never a 404, never hidden, never an empty page.
- **Empty Section:** if a Section somehow has only stubs, its index still reads intentionally (intro + topic list). No Section ships with zero Topics.
- **Search no-results:** Hextra's "no results" — keep on-brand, suggest browsing the sidebar.
- **Hover/focus:** cards lift; every interactive element has a visible focus ring.
- **No-JS:** all reading + sidebar links work as static HTML; search, theme-toggle, TOC scroll-sync are progressive enhancements that degrade gracefully.
- **404:** inherited friendly on-brand page.

## Interaction Primitives

- Sidebar expand/collapse; current-page auto-expanded on load.
- TOC scroll-sync (active heading highlighted); disabled effects under `prefers-reduced-motion`.
- Heading anchor links (hover reveals a copy-link affordance — Hextra default).
- Code-block copy buttons (Hextra default).
- Card hover lift (`translateY ~3px` + border brighten); reduced-motion drops it.

## Accessibility Floor

Target **WCAG 2.1 AA** (fitting — a11y is a Handbook topic; practice what we preach).

- **Contrast:** inherits the parent site's AA-verified tokens; the amber stub badge text `#ffd591` on `{colors.surface-2}` passes AA (12.2:1) in **dark** mode. **Build requirement:** the stub badge + Go Deeper label tints need theme-aware companions for **light** mode — the bright tints fail on white (see `DESIGN.md` Colors → theme-aware accent text). Verify in the light theme before launch.
- **Real controls, not spans:** sidebar items, breadcrumbs, search, theme toggle, prev/next, Go Deeper links are real `<a>`/`<button>` with labels and visible focus rings — use Hextra's built-ins.
- **Keyboard:** sidebar tree, TOC, search, and all in-page links reachable/operable by keyboard; logical tab order; a **skip-to-content** link past the sidebar.
- **Semantics:** one `<h1>` (the Topic title) per page; logical H2/H3 order (also makes the auto-TOC correct); landmark regions (`header`/`nav`/`main`/`aside`/`footer`).
- **Images/diagrams:** any image or mermaid diagram in a Topic needs alt text / a text description (front-matter or inline) — doubly important on an a11y-preaching site.
- **Motion:** honor `prefers-reduced-motion` (drop hover lifts, TOC/scroll animation).
- **Links:** external Go Deeper links are descriptive (not "click here") and marked as leaving the site where helpful.

## Key Flows

### Flow 1 — Carrie re-finds the levers in ten seconds (climax: "one search, not bookmark archaeology")
1. Mid-planning-debate, Carrie hits ⌘K on carriekroutil.com and types "levers."
2. The *Scope, resources & time* Topic ranks first (title + heading match).
3. She opens it, copies the framing, drops it in the discussion. **Climax:** the answer was one search away. She never touches the old wiki again. Realizes UJ-1.

### Flow 2 — Jordan, newly promoted, starts at the beginning (climax: "a path, not 40 tabs")
1. Jordan gets the link from Carrie (his coach); lands on **Start Here**.
2. Sees the guided path: *The Transition → Managing Yourself → People & Leadership → …*, each step a card with a one-line "why."
3. Reads *The Transition*; at the bottom, prev/next carries him to *Managing Yourself*. Each ends with a Go Deeper shortlist; he bookmarks one book. **Climax:** orientation + a next step, not a firehose.
4. **Resolution:** he returns over weeks, moving down the path, dipping into reference topics as real situations hit. Realizes UJ-2.

### Flow 3 — Sam, a director, deep-links from a search engine (climax: "got the gist + vetted sources, left happy")
1. Sam googles "inclusive interviewing checklist," lands deep on the *Hiring & Team Building → Inclusive interviewing* Topic.
2. Reads the framing + body; the page is a **stub**, but the intro frames it and the Go Deeper block gives three credible sources. **Climax:** even the stub wasn't a wasted visit. He never needed the landing page. Realizes UJ-3.

### Flow 4 — Carrie expands a stub on a weekday night (climax: "edited one file, pushed, it's better")
1. GoatCounter shows the *On-call* stub is getting traffic. Carrie opens `content/handbook/operational-excellence/on-call.md`.
2. Writes the body under the existing intro; flips `stub: false`; keeps/extends the Go Deeper block.
3. `git push` → Amplify builds → the badge's gone and the Topic's live. **Climax:** the bookmarked URL just got better, no restructure needed.

---
*Spines win on conflict with any mock, wireframe, or theme default. Visual tokens: see `DESIGN.md`.*
