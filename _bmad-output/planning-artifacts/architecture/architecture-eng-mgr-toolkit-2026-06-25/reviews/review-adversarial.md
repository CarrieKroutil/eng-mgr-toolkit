---
type: adversarial-review
target: ../ARCHITECTURE-SPINE.md
reviewer: adversarial-reviewer
method: 'construct concrete divergence pairs — two units one level down that each obey every AD to the letter yet build incompatibly'
created: '2026-06-25'
verdict: 'Conditional — spine is well-shaped but AD-15/AD-17/AD-16/AD-19 each leave a hole an honest author can fall through; fix before story-writing.'
---

# Adversarial Review — Engineering Manager Handbook Architecture Spine

## How to read this

I am not auditing prose quality. I am trying to **break the spine**: for each invariant, I build two concrete units one level down — two topic authors, or one story vs. another — that **each obey every AD to the letter** and still produce work that cannot be merged: clashing data shapes, two owners of one thing, conflicting paths, divergent rendering. Every such pair is a hole. The fix is always a new or tightened AD (or a tightened Convention row), never a code patch.

The spine's whole job (its own framing, line 22) is to fix "the calls two topic authors could otherwise make incompatibly." So the bar is: *given only this spine, AD-15..AD-20 and the inherited ADs, could two diligent people diverge?* Below, repeatedly, yes.

---

## Severity legend

- **CRITICAL** — produces a broken or visibly-inconsistent build, or two-owners-of-one-truth; will bite at launch.
- **HIGH** — silent divergence between authors/topics that a reader or maintainer will notice; not build-breaking.
- **MEDIUM** — under-specification that forces a story author to guess; recoverable but should be pinned.
- **LOW** — latent, narrow, or already half-covered.

---

## DIVERGENCE PAIRS

### PAIR 1 — Go Deeper data shape is *not* a contract: free-markdown vs. structured params (CRITICAL)

**The two units.** Story A implements the Go Deeper shortcode (AD-17) and the *On-call* topic; Story B (or author B) implements the *Scope, resources & time* topic and invokes the same shortcode.

**The AD they both obey.** AD-17 says: "A project shortcode in `layouts/shortcodes/` renders the Go Deeper card: a '📚 Go Deeper' heading and **Books · Courses · Tools** groups, each group shown only when populated, each item a title-link + one-line 'why.'" That describes the *rendered output*. It says **nothing about how the author passes the data into the shortcode** — and a Hugo shortcode can take its data three completely different, mutually-incompatible ways:

1. **Inner-content markdown** — `{{< go-deeper >}} ...nested markdown lists... {{< /go-deeper >}}` (author hand-writes the Books/Courses/Tools lists inside).
2. **Positional/named params** — `{{< go-deeper book1="Title|url|why" course1="..." >}}`.
3. **A front-matter data block** — the shortcode reads `.Page.Params.goDeeper.books[]` etc., and the author writes a YAML map in front matter; the shortcode is parameterless in the body.

**The incompatibility.** All three render the *same visual card* and all three "obey" AD-17 verbatim — but they are three different authoring contracts and three different shortcode implementations. If Story A builds the shortcode to read a front-matter block (option 3) and author B writes nested markdown (option 1), author B's topic renders an **empty Go Deeper card** (or the raw text dumps below it) — and on a stub that silently violates AD-18's "every stub carries a Go Deeper item." Worse: this is exactly the highest-divergence surface AD-17 claims to be closing, and it leaves the parameter shape — the actual contract — unspecified. The mock (`topic.html`) shows the rendered HTML but not the source authoring form, so it does not disambiguate.

**Fix — tighten AD-17 (or add AD-17a):** pin the **authoring signature** of the shortcode, not just its output. State explicitly which of {inner-markdown, named-params, front-matter data block} is canonical, with the exact item schema. Recommended (consistent with parent AD-3's "structured data in front matter, derived rendering" philosophy): a front-matter `goDeeper` map of three optional arrays `books|courses|tools`, each item `{ title, url, why }` — see PAIR 2 for that schema. Then the shortcode body is `{{< go-deeper >}}` with no args, and there is exactly one way to author it.

---

### PAIR 2 — Go Deeper item schema (the per-item fields) is unpinned (CRITICAL)

**The two units.** Author A writes a Books entry; author B writes a Books entry; both for migrated topics.

**The AD they both obey.** AD-17: "each item a title-link + one-line 'why.'" That names *two* conceptual fields (a linked title, a why) but pins **no field names, no link/title separation, and no rules for the link**:

- Author A: `{ title: "High Output Management — Andy Grove", url: "https://...", why: "the one to read first" }` — author in title, separate `url`.
- Author B: `{ name: "High Output Management", author: "Andy Grove", link: "...", note: "...", year: 1983 }` — splits author into its own field, renames `title`→`name`, `url`→`link`, `why`→`note`, adds `year`.

**The incompatibility.** Same AD, two different maps. A single shortcode template cannot render both: it reads `.title`/`.url`/`.why` (or whatever it was coded for) and silently drops author B's `name`/`link`/`note`. Every author B item renders blank or unlinked. There is also no rule on **where the author name lives** — inside the title string (mock State A: "High Output Management — Andy Grove") or as a field — so even two authors using the *right* field names diverge on what goes in `title`.

Also unpinned: **external-link behavior.** EXPERIENCE.md §Component Patterns says Go Deeper external links "open in a new tab with rel noopener," and Accessibility says they must be descriptive and marked as leaving the site. The spine's AD-17 never states this, so two shortcode implementations diverge: one adds `target="_blank" rel="noopener"`, the other doesn't. (Spine wins over the mock/EXPERIENCE per the spine's own framing, so the *spine* must carry this or it's a coin-flip.)

**Fix — pin the item schema inside AD-17 (or the new AD-17a from PAIR 1):** exact field names (`title`, `url`, `why`), what belongs in `title` (work + author, or split — pick one), `url` required-or-optional (and if optional, the no-link render), and the mandatory `target="_blank" rel="noopener"` + "opens externally" affordance for outbound links. One schema, one renderer.

---

### PAIR 3 — Group labels: AD-17 pins three, the mocks ship five, FR-5 implies grouping is optional (HIGH; CRITICAL for the spine-vs-mock contradiction)

**The two units.** The *Scope, resources & time* topic (mock State A) and the *On-call* stub (mock State B) — both shipped as the visual contract.

**The AD they both obey.** AD-17 pins **exactly** "Books · Courses · Tools" groups. But:

- Mock **State A** (full topic) uses groups labeled **"Books"** and **"Articles"** — *Articles* is not one of the three AD-17 groups.
- Mock **State B** (stub) uses **"Books," "Courses / Free," "Tools"** — *"Courses / Free"* is not the bare "Courses" label AD-17 pins.
- PRD FR-5 tags grouping itself as `[ASSUMPTION]` and says items "may be grouped by type … when more than ~3 exist" — i.e. grouping is *conditional*, contradicting AD-17's "groups, each shown only when populated" (which implies always-grouped).

**The incompatibility.** Two authors reading the same source set diverge: author A (following the mock) creates an "Articles" group → the shortcode, if it only knows `books|courses|tools`, **drops it**. Author B (following AD-17) puts the same article under "Courses" or omits it. The reader sees inconsistent group taxonomies across topics — the exact "structural drift across every page" AD-17 exists to prevent. The mock literally violates the AD it cites as governing. Either the group set is wrong in AD-17 (handbooks really do want an Articles/Free bucket) or the mocks are wrong — but the spine must be the single source and the mocks must be brought into line (spine wins on conflict, so the mocks are currently *known-wrong* and will mislead the story author who treats them as a build reference).

**Fix — reconcile AD-17 against the mocks and FR-5:** decide the canonical, *closed* group set (likely `Books · Courses · Tools`, or extend to add `Articles`/`Free` if intended) and state grouping is **always-on, populated-only** (kill FR-5's "when >3 exist" conditional). Add a build note that the mock labels ("Articles," "Courses / Free") must be corrected to match. A closed enum means the shortcode can reject an unknown group at build time rather than silently dropping it.

---

### PAIR 4 — Section folder slugs are never pinned; "The Transition" can be slugged two ways → guided-path links break (CRITICAL)

**The two units.** Author A creates Section 1's folder; author B (or the Start Here story) writes the guided-path link to Section 1.

**The AD they both obey.** AD-16 fixes the **12-Section ordered sequence by display name** ("The Transition → Managing Yourself → …") and sets `weight 1..12`. The Convention row says "slug = folder name (inherited AD-4)." But **the mapping from the 12 display names to actual folder slugs is nowhere pinned.** "The Transition" can legitimately become:

- `the-transition/`
- `the-transition-engineer-to-manager/` (the EXPERIENCE.md heading is "The Transition (Engineer → Manager)")
- `transition/`
- `1-the-transition/` (author front-loads the weight into the slug)

The Structural Seed *shows* `the-transition/`, `managing-yourself/`, `engineering-foundations/` — but the seed explicitly says "scaffold, not a mirror to maintain… the code owns the detail," so it is **illustrative, not binding.** Section 12 alone has three names in play: "Engineering Foundations / Must-Knows" (AD-16), "Engineering Foundations" (sidebar mock), and the slug `engineering-foundations/` (seed) — a `/` in the AD-16 name is not even a legal single slug.

**The incompatibility.** The Start Here guided path (AD-19) and the section grid link **by path** (`/handbook/the-transition/`). If author A slugs the folder `the-transition-engineer-to-manager/` and author B writes the Start Here card pointing at `/handbook/the-transition/`, the guided-path link **404s** — directly defeating AD-19 and the "nothing 404s" launch promise (AD-18/FR-10). This is two-owners-of-one-string with no canonical source.

**Fix — add an AD (or a binding Convention table) pinning the Section name → slug map** for all 12 sections (e.g. `the-transition`, `managing-yourself`, …, `engineering-foundations`), and forbid weight-prefixed slugs (weight lives only in front matter, AD-16). Make this table normative ("binding, not scaffold"), unlike the Structural Seed. Resolve the Section 12 name to a single canonical display + single slug.

---

### PAIR 5 — Guided path links to topics by path, but topic slugs are author-owned and mutable → no stable-link contract (CRITICAL)

**The two units.** The Start Here story (AD-19) which links the guided path to specific *topics*; and a topic author (AD-15/AD-16) who owns that topic's slug (= folder name, AD-4) and weight.

**The AD they both obey.** AD-19 renders "guided-path **step cards**" that link into the path. AD-15 lets the author set `title`/`weight`; AD-4 (inherited) makes the **slug = the topic's folder name**, author-chosen and human-readable, and explicitly **mutable** (rename the folder → URL changes). There is **no stable-ID or link contract** between the Start Here cards and the topics they target.

**The incompatibility.** The path (FR-3) links *The Transition → Managing Yourself → People & Leadership → Performance & Growth → Hiring & Team Building*, then branches. If the Start Here author hard-codes `/handbook/the-transition/your-first-90-days/` and the topic author later renames the folder to `first-90-days/` (a legitimate AD-15-obeying edit — AD-15 doesn't freeze slugs), the path card **404s**, silently, on the flagship newcomer surface. Same failure mode as PAIR 4 but at topic granularity, and harder to catch because path cards may point at *deep* topics, not just sections. The spine has no "links to a guided-path target must use a stable reference" rule, and Hugo offers `ref`/`relref` (build-fails on a missing target) precisely for this — but nothing in the spine *requires* it, so author B can hard-code a string instead.

**Fix — add an AD:** all internal Handbook links that participate in navigation (Start Here path cards, section grid, any cross-topic link) **must** use Hugo `ref`/`relref` (or a pinned logical path), so a renamed/missing target **fails the build** instead of shipping a 404. Optionally: define whether the guided path targets *sections* or *specific topics* (FR-3 + the mock are ambiguous — the mock's path cards name Sections, but "reads the first two in order" in UJ-2 implies topics). Pin the granularity, then pin the stable-reference rule.

---

### PAIR 6 — Sidebar `weight` ties have no tie-break (HIGH)

**The two units.** Two topics in the same Section — say *Coaching skills* and *Giving feedback* under Performance & Growth — authored independently, each given `weight: 10`.

**The AD they both obey.** AD-16: "Topics order by their own `weight` within a Section." Both set `weight: 10`. Legal under AD-15 (weight is just an int) and AD-16 (both have a weight).

**The incompatibility.** Hugo's `.Pages.ByWeight` falls back to **`Date` then `LinkTitle` then file path** on equal weight — but `date` is *forbidden/derived* here (AD-15 says no date-gating; last-updated is git-derived, not a front-matter `date`), so the effective tiebreak is title/path order, which is **non-obvious and unstable** (rename a file → order flips). Two authors who both reach for a round `weight: 10` get a sidebar order *neither chose*, and it can change under them. AD-16 claims to prevent "two topics racing for one slot" but provides no resolution when they do.

**Fix — tighten AD-16:** require **unique weights within a Section** (state it as an invariant: "no two siblings share a weight"), and recommend a spacing convention (10, 20, 30…) so inserts don't force renumbering. Optionally state the deterministic tiebreak if a collision somehow ships (e.g. by `title` ascending) — but the real fix is "weights are unique per Section," enforceable by a lint.

---

### PAIR 7 — `{topic}/index.md` (bundle) vs `{topic}.md` (flat) — the spine and EXPERIENCE.md disagree on the content unit (CRITICAL)

**The two units.** Author A migrates *Coaching skills* as a page bundle `coaching-skills/index.md`; author B creates the *Giving feedback* stub as a flat file `giving-feedback.md`.

**The AD they both obey.** AD-15 says "Every topic `index.md`" and the Structural Seed shows `{topic}/index.md` (page bundle). But **EXPERIENCE.md §Foundation (line 24) and §IA (line 37) both specify `content/handbook/{section}/{topic}.md`** — a *flat file*, not a bundle. The spine's own Capability Map and AD-14 say URLs derive from "the content tree." Both a bundle (`coaching-skills/index.md`) and a flat file (`giving-feedback.md`) produce the URL `/handbook/{section}/{topic}/` in Hugo, so **both authors get a working URL and both believe they followed the contract** — A followed the spine, B followed EXPERIENCE.md.

**The incompatibility.** (a) Inconsistent repository structure — half the topics are folders, half are files — which breaks any tooling/lint that assumes one shape, and breaks the "images live beside the markdown in a bundle" affordance (EXPERIENCE.md Accessibility says topics may contain images/diagrams needing alt text; a flat `.md` file has nowhere to put a co-located image). (b) It is a **spine-vs-inherited-design contradiction the spine is supposed to surface, not leave live**: the spine binds DESIGN.md/EXPERIENCE.md as sources but silently diverges from EXPERIENCE.md's content-model line.

**Fix — tighten AD-15 to pin the unit explicitly** ("every topic is a Hugo **page bundle**: `{section}/{topic}/index.md`; flat `{topic}.md` is not permitted, so topic images co-locate per inherited AD-9") **and** flag the EXPERIENCE.md `{topic}.md` line as a contradiction to correct (spine wins). Without this, every author coin-flips bundle vs flat.

---

### PAIR 8 — `description` field: required by EXPERIENCE.md, absent from AD-15 (HIGH)

**The two units.** Author A writes a topic with a `description` front-matter field (for SEO/meta); author B omits it. Both "obey AD-15."

**The AD they both obey.** AD-15 lists the canonical front matter as `title`, `weight`, optional `stub`, plus git-derived last-updated — and calls itself "the canonical topic front-matter contract" whose purpose is to stop topics "diverging on field names." It does **not** list `description`. But **EXPERIENCE.md §Foundation (line 24) lists `description` (meta/SEO)** as part of the content model, and FR-13/parent conventions care about discoverability (OG/meta).

**The incompatibility.** AD-15 claims to be *the* contract ("Every topic carries…"), so author B reasonably omits `description` (it's not in the canonical list) → that topic ships with Hugo's auto-summary or an empty meta description, while author A's topics have curated ones. Inconsistent SEO/OG output across the Handbook, and the "canonical contract" is not actually canonical because a real, expected field lives outside it. This is a field-name/shape divergence — exactly AD-15's stated target.

**Fix — reconcile AD-15 with EXPERIENCE.md:** either add `description` (optional, with stated fallback behavior) to the AD-15 field list, or explicitly state it's intentionally omitted and EXPERIENCE.md's line is superseded. Make AD-15 genuinely exhaustive so "not in the list" reliably means "not a field."

---

### PAIR 9 — A stub can ship without a Go Deeper item: nothing enforces AD-17's "≥1 item" (HIGH)

**The two units.** Stub author A writes *AI literacy for EMs* with a populated Go Deeper; stub author B writes *Compliance literacy* and invokes the shortcode but populates **zero** items (e.g., writes `{{< go-deeper >}}` with an empty/absent data block, intending to fill it later).

**The AD they both obey.** AD-18: a stub "carries its Go Deeper block exactly like a full topic." AD-17: the shortcode is present and "each group shown only when populated." Author B's topic **has the shortcode** (block present, AD-18 satisfied) and **renders no groups because none are populated** (AD-17's "shown only when populated" satisfied). Both ADs are obeyed to the letter. AD-17 also says "carrying **≥ 1** curated item" — but nothing **enforces** it: with all three arrays empty, the shortcode renders just the "📚 Go Deeper" heading over emptiness, and the build still succeeds.

**The incompatibility.** A stub ships with an **empty Go Deeper card** — precisely the SM-C3 failure ("a Stub with no useful curation is worse than no page") and a direct violation of FR-5's "at least one curated item." The "≥1 item" is stated as a rule but is structurally unenforceable given AD-17's "populated-only" rendering: emptiness is indistinguishable from "all groups happen to be empty." Parent AD-9 shows the team's pattern is **build-failing enforcement** for invariants that matter (empty alt fails the build); AD-15's `date` rule fails the build; but the Go Deeper "≥1 item" rule has **no such teeth**.

**Fix — give AD-17's "≥1 item" build-failing teeth:** the shortcode (or a build check) must **error the build** when a topic's Go Deeper has zero populated items across all groups — mirroring parent AD-9's empty-alt hook. State it in AD-17 as "an empty Go Deeper fails the build," so a stub literally cannot ship without curation.

---

### PAIR 10 — Stub badge: AD-18 says shortcode/partial renders it; an author can hand-write a Markdown badge instead (MEDIUM)

**The two units.** Stub author A sets `stub: true` and lets the layout render the "🚧 Expanding" badge (AD-18). Stub author B *also* sets `stub: true` **and** types a literal "🚧 **Expanding**" line at the top of the body (copying the mock's microcopy, which shows both a badge *and* an inline "🚧 Expanding" paragraph — mock State B has both).

**The AD they both obey.** AD-18: "`stub: true` is the only stub mechanism. It renders an inline '🚧 Expanding' badge — a non-heading element." Author B still set `stub: true` (only mechanism: ✓) and the badge renders; B *additionally* hand-wrote a duplicate. AD-18 forbids `draft:true` as an alternate flag but does not forbid hand-authored badge markup in the body. EXPERIENCE.md Voice/Tone actually *prescribes* an honest intro microcopy line ("🚧 **Expanding** — the deep-dive's still cooking…"), and the mock shows it — so author B is following EXPERIENCE.md, while the auto-rendered badge is from the layout. Result: **the badge appears twice** on B's page (once as the title pill, once as the prose line), inconsistent with A's single pill.

**The incompatibility.** Divergent stub rendering: A shows one amber pill by the title; B shows the pill *and* a duplicate inline "Expanding" prose block. Both set `stub:true`. The "🚧 Expanding" string now has two owners (the layout partial and the author's prose) with no rule on which renders the badge vs. the framing sentence. The mock conflates them; the spine doesn't separate them.

**Fix — clarify AD-18:** distinguish the **badge** (layout-rendered from `stub:true`, the title pill — the *only* place the badge token lives) from the **stub framing microcopy** (author prose, which should *not* repeat a "🚧 Expanding" badge-like line). State that the literal badge is never hand-authored in the body. This keeps one owner for the badge.

---

### PAIR 11 — Start Here layout selection: `layouts/handbook/_index.html` vs `_default` override scoped by `type=docs` (MEDIUM)

**The two units.** The Start Here story (AD-19) and the section-index behavior (EXPERIENCE.md Surface 2).

**The AD they both obey.** AD-19: "a custom layout override (in `layouts/`)" renders Start Here. The Structural Seed offers two *alternative* placements with an explicit "OR": `layouts/handbook/_index.html` **OR** a `_default` override scoped via `type=docs`. These are not equivalent: a `_default/_index.html` scoped to `type=docs` would catch **every** docs section-index — including each of the 12 **Section** `_index.md` pages — and turn *them* into Start-Here-style step-card/section-grid pages too, which EXPERIENCE.md Surface 2 says should instead be a plain "what this section covers + topic list" (Hextra default + intro).

**The incompatibility.** If the Start Here story author picks the `_default`+`type=docs` route (option B in the seed), the custom Start Here layout **leaks onto all 12 section indexes**, breaking Surface 2; if they pick `layouts/handbook/_index.html` it applies only to `/handbook/` and section indexes keep the Hextra default. Both "obey AD-19" (it's a custom layout in `layouts/`), but they produce different section-index pages. The seed presenting an unresolved "OR" is the hole.

**Fix — resolve the "OR" in AD-19:** pin that the Start Here override applies to **only `/handbook/_index.md`** (the section root), and that the 12 Section `_index.md` pages use the **default docs section-index** (+ author intro), so the custom layout cannot leak. Remove the ambiguous `_default`/`type=docs` alternative from the seed (or state it must be scoped to depth-1 only).

---

### PAIR 12 — "EM Handbook" vs "Handbook" menu label (LOW)

**The two units.** The `hugo.toml` menu story and the PRD glossary.

**The AD they both obey.** AD-14 adds one `[[menu.main]]` entry, label **"EM Handbook"** but tags it `[ASSUMPTION]` and notes "PRD glossary says 'Handbook'; UX revised to 'EM Handbook'." The PRD and `.memlog` say nav label "Handbook"; EXPERIENCE.md §IA says "EM Handbook" (placed first).

**The incompatibility.** Minor and single-owner (only one menu entry, one file) — but two source docs disagree and the spine leaves it `[ASSUMPTION]`, so a story author could ship either. Not a build break, not multi-author divergence (one menu string), hence LOW. Listed for completeness.

**Fix — drop the `[ASSUMPTION]`:** confirm "EM Handbook" (EXPERIENCE.md's reasoned choice, placed first) as the binding label and note the PRD glossary is superseded. One string, one source.

---

## Secondary observations (not full divergence pairs, but soft spots)

- **`enableGitInfo` availability is an `[ASSUMPTION]` with a fallback (`lastmod`) — but no rule on who sets `lastmod` or its format.** If the git-info assumption fails at build, AD-15's fallback is a front-matter `lastmod`, yet `lastmod`'s field name/format/whether-every-topic-needs-it is unspecified → if two authors fall back differently (one `lastmod`, one `date`, one none), last-updated renders inconsistently. Pin the fallback field name + format now so it's not invented under pressure. (MEDIUM-ish, contingent.)
- **`maxSectionResults=1` is tagged `[ASSUMPTION] (confirm)`** for "one result per topic" — if it's actually per-*section*, every Handbook topic in a section could collapse to one search hit, breaking FR-7's "a single Topic returns as one result" (which wants one-per-topic, not one-per-section). Verify the semantics; the names suggest section-scoping, which would be wrong for this requirement. (Inherited config; flag to verify.)
- **`cascade {type: docs}` as the Hextra v0.12.3 docs-mode lever is itself `[ASSUMPTION]` (AD-14).** If wrong, the entire docs-mode projection (sidebar + TOC) fails — this is the spine's load-bearing assumption and should be verified against a local build before any story depends on it. (Not a divergence pair, but the highest-leverage unverified call.)
- **Pro-tip callout (EXPERIENCE.md component) has no AD.** It's a Hextra-native callout, so likely fine, but two authors could use different callout variants/syntax (`{{< callout >}}` type=info vs tip) → visual drift on "Pro Tip" asides. Low risk; consider a one-line Convention row pinning the callout variant.

---

## Summary of fixes (what to add/tighten before story-writing)

| # | AD touched | Fix | Severity |
| --- | --- | --- | --- |
| 1 | AD-17 (+ new AD-17a) | Pin the shortcode **authoring signature** (front-matter data block, not inner-markdown/params) | CRITICAL |
| 2 | AD-17 | Pin per-item **schema** (`title`,`url`,`why`) + outbound-link `target/rel` + "what goes in title" | CRITICAL |
| 3 | AD-17 / FR-5 / mocks | **Closed** group enum; grouping always-on populated-only; correct mock labels ("Articles","Courses/Free") | HIGH |
| 4 | new AD / Convention | Binding **Section-name → slug** map for all 12; forbid weight-prefixed slugs; resolve Section 12 name | CRITICAL |
| 5 | new AD | Internal nav links **must** use `ref`/`relref` (build-fails on missing); pin path-target granularity | CRITICAL |
| 6 | AD-16 | **Unique weights per Section** invariant (+ spacing convention, deterministic tiebreak) | HIGH |
| 7 | AD-15 | Pin topic unit = **page bundle** `{topic}/index.md`; forbid flat `.md`; flag EXPERIENCE.md contradiction | CRITICAL |
| 8 | AD-15 | Reconcile **`description`** field (add or explicitly exclude); make AD-15 exhaustive | HIGH |
| 9 | AD-17 | **Empty Go Deeper fails the build** (teeth for "≥1 item", mirroring parent AD-9) | HIGH |
| 10 | AD-18 | Separate **layout-rendered badge** from author stub-microcopy; badge never hand-authored | MEDIUM |
| 11 | AD-19 | Resolve the seed's **"OR"**: Start Here override scoped to `/handbook/_index.md` only | MEDIUM |
| 12 | AD-14 | Confirm **"EM Handbook"** label; drop `[ASSUMPTION]` | LOW |

**Verdict:** The spine is genuinely lean and well-aimed — it identified the right surfaces (front-matter, Go Deeper, ordering, stubs, single-home). But on its single highest-stakes surface (AD-17 Go Deeper) it specifies the *rendered output* and leaves the *authoring contract* — parameter shape and item schema — completely open, which is exactly where two authors diverge most. Section/topic **slugs** are shown but never **bound**, so the guided path (AD-19) links into strings nobody owns. And the spine silently contradicts its own inherited EXPERIENCE.md on the content unit (bundle vs flat) and the `description` field. Five CRITICAL holes, three HIGH. Close 1–9 before writing the first story; 10–12 before launch.
