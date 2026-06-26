# Rubric Walk — Architecture Spine, Engineering Manager Handbook

**Reviewer stance:** fresh, skeptical reader. Goal: find what the author talked past.
**Subject:** `ARCHITECTURE-SPINE.md` (eng-mgr-toolkit, 2026-06-25) — a FEATURE-altitude, brownfield EXTENSION spine.
**Parent:** `ARCHITECTURE-SPINE.md` (carriekroutil-site, 2026-06-22), inherited read-only (AD-1..AD-13).
**Reality-check:** verified against `carriekroutil-site` on disk (HEAD `854299c`, 2026-06-25).

**Verdict: PASS-WITH-FIXES.** The spine is well-structured, honest about its inheritance, and fixes the genuine divergence surfaces for topic authors. It earns its claim of being reality-checked — most named tech is confirmed. But two of its load-bearing "inherited / already-true" assertions are wrong against the actual repo: `enableGitInfo` is **not** present (AD-15's last-updated mechanism does not exist yet and must be authored), and `maxSectionResults=1` was deliberately set *for the blog* to suppress per-heading results — exactly the behavior a multi-heading docs topic wants, so treating it as a free inherited win is a talked-past tension. Neither is fatal; both need a one-line correction so an epic author doesn't ship on a false premise.

---

## Checklist 1 — Does it fix the real divergence points for the level below, and miss none?

**Strong PASS.** The spine correctly identifies the highest-divergence surfaces for 40+ independently-authored topic pages and nails them:

- **Front-matter contract (AD-15)** — the single biggest divergence risk; field names, stub signal, ordering all pinned.
- **Sidebar ordering (AD-16)** — the 12-section locked sequence + per-topic `weight`, with "no orphans."
- **Go Deeper as a shortcode not free markup (AD-17)** — kills per-topic markup drift, the second-biggest risk.
- **Stub mechanism (AD-18)** — one flag, never `draft:true`; this is the subtle trap an author *would* fall into, and the spine catches it.
- **Content single-home (AD-20)** — prevents the two-repo divergence that this brownfield split actively invites.

What an author still needs and *gets*: topic anatomy order (Conventions), URL derivation (AD-14 + inherited AD-4), accent token reuse (AD-17/Conventions), long-page handling (Conventions). Nothing in the "topic author" decision space is silently left open.

One small gap: **what a Section `_index.md` body contains** is unspecified. AD-16 governs its `weight`, AD-19 governs the *handbook root* `_index.md`, but a per-Section landing (e.g. `the-transition/_index.md`) — does it render a topic list, an intro, nothing? Hextra docs mode auto-generates a section index, so this is probably fine by inheritance, but two authors could write Section intros of wildly different shape. **Low severity** — worth one Conventions row ("Section `_index.md` carries title + weight only; body optional, Hextra auto-lists topics").

## Checklist 2 — Is every AD's Rule enforceable and does it prevent its stated divergence?

Mostly PASS, with two enforceability soft spots:

- **AD-15** — "Last updated derived from git via `enableGitInfo`." This is enforceable *only after* `enableGitInfo` is turned on, and it is **not currently in `hugo.toml`** (verified). The Rule names the fallback (`lastmod` front matter) and tags the assumption, which is good hygiene — but as written it reads as if the mechanism exists. See Checklist 4/5. The *contract* (YAML, title/weight/stub) is fully enforceable. **Medium.**
- **AD-17** — "≥ 1 curated item on every topic incl. stubs." A shortcode can render an empty Go Deeper if an author invokes it with no items; nothing *mechanically* fails the build on zero items (unlike the parent's alt-text build-fail hook in AD-9). The Rule states the invariant but doesn't give it teeth. Given SM-C3 ("a stub with no useful curation is worse than no page") this is a real, named risk the architecture leaves to discipline. **Low–Medium** — consider a render-time `errorf` in the shortcode when all groups are empty, mirroring the parent's build-fail posture.
- **AD-14, AD-16, AD-18, AD-19, AD-20** — Rules are concrete, file-located, and each genuinely blocks its stated divergence. AD-18's "non-heading element so it never disturbs the auto-TOC" is a precise, testable invariant. Good.

## Checklist 3 — Could anything under Deferred let two units diverge?

**PASS.** The four deferred items are all genuine non-divergence pushdowns:

- Tags/taxonomy (FR-8 retired) — absence can't cause divergence; it's a uniform "no tags in the Handbook."
- Interactive tools / feedback / RSS — out of scope entirely.
- Broken-link audit — a migration-time content QA task, not a structural choice two authors decide differently.
- Light-mode AA re-verification — a verify-once gate, not a per-unit decision.

Note: the Deferred light-mode item is **stale** — see Checklist 5. It says "re-verify before relying on light mode (inherited parent deferral)," but the parent's light-mode AA deferral has since been **closed** in the repo (the `html:not(.dark)` AA-tuned block ships, chip tokens included). This doesn't cause divergence, so it passes this check, but the spine is citing a deferral that no longer exists.

## Checklist 4 — Is named tech verified-current against the on-disk repo?

**Mostly PASS — one item wrong, one item under-stated.**

Verified correct against `carriekroutil-site` HEAD:
- Hugo extended **0.163.3** — matches `amplify.yml` exactly. ✓
- Hextra **v0.12.3** locked in `go.mod` — exact. ✓
- Go `1.25.3` directive — matches `go.mod` (`go 1.25.3`). (The spine also says "1.26.4 in build" — not verifiable from the repo; harmless.) ✓
- AWS Amplify + Route 53 — consistent with `amplify.yml` and parent. ✓
- FlexSearch `index=content`, `maxSectionResults=1` — both present in `hugo.toml`. ✓ (but see Checklist 8 for the *semantics*)
- `--ck-chip-{violet,fuchsia,amber}-text` tokens exist in `custom.css`. ✓
- Existing `ckbutton`/`ckbuttons` shortcodes exist (AD-17's "on-pattern" claim). ✓ — `layouts/shortcodes/` confirmed.

**Wrong / not-yet-true:**
- **`enableGitInfo`** (AD-14 structural seed comment, AD-15) — **NOT present in `hugo.toml`** (verified by grep). The Structural Seed annotates `hugo.toml` with "+ enableGitInfo" which correctly signals "to be added," but AD-15's prose ("derived from git via Hugo `enableGitInfo`") reads as an existing capability. An epic author could assume last-updated works out of the box. **Medium** — state plainly that `enableGitInfo = true` is a *new* config line this feature adds, and confirm git history survives the Amplify shallow clone (the spine's `[ASSUMPTION]` already flags the latter — good).

**Under-stated reality (the AA claim):** AD-17 says the chip tokens are "already AA-paired for light mode," and Deferred says to "re-verify... before relying on light mode." The repo shows the light-mode AA work is **done and shipped** (`html:not(.dark)` block, `--ck-chip-*-text` darkened to `#6d28d9 / #a21caf / #92400e` with documented ratios). So AD-17 is *more* right than the Deferred section admits — the two contradict each other. **Low** — reconcile: the chip tokens are AA-paired *and shipped*; only the **stub-badge** color (`#ffd591`, a UX `DESIGN.md` addition, not yet in `custom.css`) and the Go Deeper *label application in docs-mode surfaces* still need a light-mode verify.

## Checklist 5 — Does it RATIFY rather than contradict the brownfield codebase?

**PASS with the corrections above.** The spine respects the real repo's structure: project-`layouts/` overrides only (AD-5 honored), no module edits, reuses existing tokens and shortcode patterns, adds no dependency. The dependency-direction diagram matches the parent's. The one substantive contradiction is the **stale light-mode deferral** (Checklist 4): the spine inherits a parent deferral that the parent repo has already resolved. Ratify the closed state instead of re-opening it.

A second, subtler ratification gap: **AD-14's framing of parent AD-4.** AD-14's "Prevents" says parent AD-4 "*replaced* Hextra's blog layouts with custom overrides … losing the native docs sidebar + TOC." The repo confirms posts use section-name-routed custom `layouts/posts/{single,list}.html` and set **no** `type` — so the docs chrome genuinely isn't in play for posts, and `cascade:{type:docs}` on `/handbook` is correctly scoped. The framing is accurate. ✓ The only open verification is AD-14's own `[ASSUMPTION]` that `cascade:{type:docs}` is the right Hextra v0.12.3 lever for per-section docs mode — **this is the single most load-bearing unverified claim in the spine** (everything — sidebar, TOC, search scoping — rides on it) and is correctly flagged for a local-build check. **Medium** until proven on a build; it is appropriately tagged, so this is a "must close before epics," not a defect.

## Checklist 6 — Does it cover the PRD's capabilities (FR-1..FR-13, FR-8 retired)?

**PASS.** The Capability→Architecture Map enumerates FR-1,2,3,4,5,6,7,9,10,11,12,13 — every live FR — each with a "lives in" and a governing AD. FR-8 correctly absent (retired). Spot-checks hold:
- FR-3 (guided path + "experienced jump" affordance) → AD-19, and AD-19 explicitly carries the "experienced? jump to reference/search" affordance the PRD's FR-3 consequence demands. ✓
- FR-10 (complete IA, net-new sections + must-knows as stubs) → AD-16 + AD-18. ✓
- FR-13 (GoatCounter prod-only + sitemap/robots) → inherited; `enableRobotsTXT` confirmed in repo. ✓

PRD §9 explicitly hands the architecture two decisions: "Go Deeper as reusable convention — likely a shortcode" → **decided** as AD-17. "Long-page granularity (Open Q 2)" → addressed in Conventions ("split only when a sub-part earns its own URL/search hit"). Both PRD hand-offs are resolved.

## Checklist 7 — Does any new AD (AD-14..AD-20) weaken or contradict an inherited parent AD?

**PASS.** Each new AD is consistent with, and often explicitly anchored to, a parent AD:
- AD-14 honors AD-4 (URL from tree) and AD-5 (override location).
- AD-15 mirrors AD-3 (YAML, derived metadata) and respects AD-3's draft-exclusion semantics.
- AD-17/AD-18/AD-19 all live in project `layouts/` per AD-5; AD-18 is *built precisely to avoid* violating AD-3 (won't use `draft:true`).
- AD-20 reinforces AD-1 (write-markdown→push) and AD-2/AD-11 (single pipeline).
No new AD relaxes the no-JS-core (AD-13), the single-pipeline (AD-2), or the authoring loop (AD-1). The numbering continues cleanly from 13. Inheritance integrity is the spine's strongest quality.

## Checklist 8 — Is every dimension this altitude owns decided / deferred / open? (esp. operational envelope)

**PASS-WITH-ONE-FLAG.**

- **Operational / environmental envelope** — legitimately INHERITED and *explicitly* addressed: the Inherited Invariants table maps AD-2/AD-11/AD-12 to "the entire deploy/environment envelope," and the Capability Map routes FR-12/FR-13 to inherited pipeline + analytics. This is the right move for an extension spine and is not silently dropped. ✓
- **Content model, navigation, presentation overrides, stub lifecycle, repo topology** — all decided (AD-14..AD-20). ✓
- **Search** — this is the one dimension treated too lightly. The spine asserts (Conventions + Stack + Capability Map) that inherited FlexSearch with `maxSectionResults=1` "already yields one result per topic … no Handbook-specific tuning `[ASSUMPTION]` (confirm)." The repo comment shows `maxSectionResults=1` was set **for the blog**, where multi-heading posts were returning 3× — i.e. it was tuned to *collapse* per-heading results. A docs topic is *deliberately* multi-heading (the auto-TOC, AD-15/AD-18, depends on H2/H3). So two things are unexamined: (a) is one-result-per-topic actually the desired docs behavior, or do you want deep-link-to-heading relevance for the FR-7 / UJ-1 "land on the exact framing" journey? and (b) FlexSearch `index=content` indexes rendered content — does it index stub bodies and Go Deeper items the way the topic-reference journeys assume? The spine flags `[ASSUMPTION] (confirm)` but frames it as a non-event when it's a genuine docs-vs-blog semantic question. **Medium** — promote search-behavior-in-docs-mode to an explicit Open Question to verify on the local build, alongside the `cascade:{type:docs}` check.

---

## Findings, ranked

| # | Severity | Finding | One-line fix |
|---|---|---|---|
| 1 | **High** | `cascade:{type:docs}` (AD-14) is the single load-bearing unverified lever — sidebar, TOC, and search scoping all ride on it; correctly tagged `[ASSUMPTION]` but unproven. | Build `/handbook` locally and confirm per-section docs mode renders before opening epics; make it the top Open Question. |
| 2 | **Medium** | AD-15 "last updated via `enableGitInfo`" — `enableGitInfo` is **absent** from `hugo.toml` (verified); reads as existing capability. | State it as a new config line this feature adds; keep the `lastmod` fallback for the Amplify shallow-clone risk. |
| 3 | **Medium** | Search (FR-7) under-examined: `maxSectionResults=1` was tuned to suppress per-heading results *for the blog*; docs topics are intentionally multi-heading — desired behavior unverified. | Promote to an explicit Open Question; verify one-result-per-topic + stub/Go-Deeper indexing on the local build. |
| 4 | **Low–Medium** | AD-17 "≥1 curated item" has no enforcement teeth (unlike parent AD-9's alt-text build-fail); SM-C3 names this exact risk. | Add a render-time `errorf` in `go-deeper.html` when all groups are empty. |
| 5 | **Low** | Stale/contradictory light-mode AA: Deferred re-opens a parent deferral the repo has **closed**; chip tokens ship AA-paired (`#6d28d9/#a21caf/#92400e`). AD-17 and Deferred disagree. | Ratify closed state; scope remaining verify to the new stub-badge color + Go Deeper labels on docs surfaces. |
| 6 | **Low** | Per-Section `_index.md` body shape unspecified — two authors could write divergent section intros. | One Conventions row: "Section `_index.md` = title + weight only; body optional; Hextra auto-lists topics." |

**Bottom line:** This is a competent, honest extension spine that respects its parent and fixes the right author-facing divergences. Its assumption-tagging discipline is what saves it — the two factual misses (Findings 2, 5) and the under-played search question (Finding 3) are all flagged-or-flaggable rather than buried. Close Finding 1 with a local build, correct the two reality misses, and this is a PASS.
