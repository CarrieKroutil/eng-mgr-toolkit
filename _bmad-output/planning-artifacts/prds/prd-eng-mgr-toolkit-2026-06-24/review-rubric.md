# PRD Quality Review — Engineering Manager Handbook

## Overall verdict

This is a genuinely good, right-sized PRD for a solo passion/brand project: it has a real thesis (the "three-legged blend" of people + tooling + foundations, §1), honest scope boundaries, and FRs whose consequences are mostly testable. It correctly defers tech-how and the full IA/migration map to the brief addendum without leaving downstream workflows under-fed. What's at risk is small and mechanical — a broken cross-reference to a nonexistent "Open Question 5," a retired-but-still-cited FR-8, and a few NFR adjectives that lack bounds — none of which threaten the UX/architecture/epics handoff.

## Decision-readiness — strong

The PRD states decisions as decisions rather than burying them. Tags are not hedged — they are decided "Deferred (post-v1)" with a stated rationale ("Tags are a blog convention, not a docs one," §4.4) and a retirement note for FR-8. Trade-offs are named with what's given up: SM-C2 ("Don't trade voice/judgment for coverage… an unopinionated link-dump would betray the differentiator," §7) and the Non-Goal "opinionated curation over completeness" (§5) both make the coverage-vs-judgment tension explicit and pick a side. The `[NOTE FOR PM]` at §6.2 sits at a real tension (which content must be launch-quality vs. which may stub) rather than a safe checkpoint. Open Questions §8 are actually open (broken-link audit; per-topic granularity), and the "Resolved during PRD review" trailer honestly records what was closed rather than pretending nothing was deferred. No findings.

## Substance over theater — strong

The JTBD framing (§2.1) earns its place — four distinct jobs, each tied to a downstream FR or UJ, none redundant. The differentiator (§1) is a real editorial thesis, not template-filler, and it recurs as a load-bearing counter-metric (SM-C2). The Vision statement does not swap into any other PRD: "today trapped in a flat, unsearchable GitHub wiki" and the dual Start-Here/topic-reference shape are specific to this product. NFRs are mostly product-specific (cookieless GoatCounter, no consent banner, fuchsia primary, `/handbook/...` URLs) rather than boilerplate. No persona/innovation/vision theater detected. No findings.

## Strategic coherence — strong

There is a clear thesis and the features serve it. The dual-front-door arc (guided Start Here for newcomers + searchable reference for experts) is stated in §1 and then realized consistently across FR-3 (guided path), FR-7 (search), and the UJs (UJ-2 newcomer, UJ-1/UJ-3 reference). Prioritization follows the thesis, not ease: shipping the *complete IA with honest stubs* at go-live (FR-10) is the harder, thesis-driven choice over shipping only finished pages. Success Metrics validate the thesis rather than measuring raw activity — SM-1 is "Carrie retires the old wiki," not pageviews — and SM-C1 explicitly refuses the DAU/traffic-funnel framing. Counter-metrics are present and pointed. No findings.

## Done-ness clarity — adequate

Most FRs carry testable consequences an engineer could check (FR-1 "Blog, About, Projects… continue to work unchanged"; FR-2 enumerates the exact 12 Sections in order; FR-7 "A single Topic returns as one result, not one per heading"). This is the strongest part of the PRD for downstream story creation. A few soft spots remain.

### Findings
- **medium** Stub status indicator under-specified for "done" (§4.3 FR-6) — The status indicator is tagged `[ASSUMPTION] e.g. a "🚧 Expanding" badge or note`, yet §8's resolved-list and §9 both claim the badge is *confirmed*. The contradiction leaves a story author unsure whether "badge" is the acceptance condition or still a choice. *Fix:* drop the `[ASSUMPTION]` qualifier in FR-6 to match the §8/§9 resolution, and state the testable form ("a visible 'Expanding' badge renders on every Stub").
- **low** "lightly edited" is not a testable done-condition (§4.5 FR-9) — "content ported and lightly edited" has no verifiable bound; migration stories can't tell when a page is done. *Fix:* this is acceptable as authorial judgment for a solo project, but consider a one-line done-bar (e.g., "renders cleanly in Hextra, no broken internal links, voice pass applied").
- **low** Performance NFR uses adjectives (Cross-Cutting NFRs) — "load quickly," "static-site fast" have no bound. *Fix:* fine to leave for a passion project, but a single threshold (e.g., Lighthouse perf ≥ 90, or "no client JS beyond Hextra defaults" as the actual bound — which is already stated) would close it.

## Scope honesty — strong

Omissions are explicit and do real work. The Non-Goals section (§5) is sharp and category-defining (not a course/LMS/community/newsletter/custom-app/separate-product), and §6.2 separately lists MVP deferrals with rationale. De-scoping is done openly — FR-8 retirement is announced, not silent. `[ASSUMPTION]` tags are used on genuine inferences (Go Deeper grouping, stub badge) and indexed in §9; `[NOTE FOR PM]` sits at the real launch-quality tension. Open-items density is low and entirely appropriate for the modest stakes: two open questions, two remaining open assumptions, one PM note. No findings.

## Downstream usability — adequate

The Glossary (§3) is solid and domain nouns (Handbook, Section, Topic, Stub, Go Deeper, Guided path) are used consistently across FRs, UJs, and SMs. FR/UJ/SM IDs are unique and the FR-8 gap is deliberately documented. UJs each have a named protagonist carrying context inline (Carrie/UJ-1, Jordan/UJ-2, Sam/UJ-3) — no floating UJs. Sections largely stand alone. The deductions are cross-reference breaks that a UX/epics workflow could trip on.

### Findings
- **medium** Broken cross-reference to "Open Question 5" (Information Architecture section, line 249) — "Detailed nesting/grouping within Sections is a UX-phase deliverable (see Open Question 5)" points to an Open Question that does not exist — §8 lists only two. A UX workflow source-extracting the IA hand-off will hit a dead link. *Fix:* either re-point to the actual relevant item (the per-Topic granularity question, §8 #2) or drop the parenthetical.
- **low** Retired FR-8 still cited by ID (§9, line 236) — §9 says "tags deferred (FR-8)" while §4.4 declared FR-8 "intentionally retired." Citing a retired ID as if live is mildly confusing for ID-tracking downstream. *Fix:* reword to "tags deferred (see §4.4)" without the retired ID.

## Shape fit — strong

The PRD is correctly shaped. This is a content/brand product with meaningful UX and a real reader (not a single-operator internal tool), so the named-protagonist UJs are load-bearing and appropriately present — not over-formalized. Rigor is calibrated light (qualitative SMs, "right-sized to a passion project," §7) while the substance bar holds. It correctly distinguishes new content (net-new Sections/Stubs, FR-10) from existing/migrated content (FR-9), the brownfield distinction the rubric asks for. Tech-how and the full IA/migration map are deferred to the brief addendum by design, which is the right call for a chain-top PRD whose architecture doc is still forthcoming. No findings.

## Mechanical notes

- **Glossary drift:** none material. "Topic page"/"Topic" dual form is defined explicitly in §3 and used consistently. "Start Here" vs. "Guided path" are cleanly distinguished.
- **ID continuity:** FR-1 through FR-13 contiguous with FR-8 deliberately and visibly retired (§4.4) — good practice, well documented. UJ-1..3 and SM-1..4 + SM-C1..C3 all unique. One stale ID citation (FR-8 in §9, flagged above).
- **Broken cross-ref:** "Open Question 5" (line 249) — see Downstream finding.
- **Assumptions Index roundtrip:** §9 is light but consistent — the inline `[ASSUMPTION]` tags (FR-5 Go Deeper grouping, FR-6 stub badge) are accounted for in the §9 "confirmed and folded in" line, and the two remaining open assumptions (§6 launch-quality content, Go Deeper as reusable convention) are both traceable inline. Clean enough for the stakes.
- **UJ protagonist naming:** all three UJs carry a named protagonist with inline context. Good.
- **Required sections:** all present and appropriate for the agreed stakes (Vision, Target User/JTBD, Glossary, Features/FRs, Non-Goals, MVP Scope, Success Metrics + counter-metrics, Open Questions, Assumptions Index, IA, Aesthetic/Tone, Cross-Cutting NFRs).
