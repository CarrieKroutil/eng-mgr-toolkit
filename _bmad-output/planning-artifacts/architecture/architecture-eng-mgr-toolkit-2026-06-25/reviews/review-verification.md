---
review-type: verification
target: ../ARCHITECTURE-SPINE.md
target-name: Engineering Manager Handbook — architecture spine
reviewer: verification reviewer (reality-check against repo on disk)
date: '2026-06-25'
verified-against:
  - /Users/carrie/code/carriekroutil-site (go.mod, go.sum, amplify.yml, hugo.toml, layouts/, assets/css/custom.css, content/)
  - Hextra v0.12.3 module source on disk (/Users/carrie/go/pkg/mod/github.com/imfing/hextra@v0.12.3)
  - empirical Hugo build (local hugo 0.151.2+extended, same Hextra v0.12.3 module)
verdict: APPROVE WITH MINOR FIXES
---

# Verification Review — Engineering Manager Handbook spine

**Method.** Every version/config claim was checked against the actual `carriekroutil-site`
repo on disk. The two riskiest mechanism claims (AD-14 `cascade: {type: docs}`, AD-15
`enableGitInfo`) were not just read about — they were **proven by an actual Hugo build**
against the real, version-locked Hextra v0.12.3 module, then the test artifacts were fully
removed and the repo restored to its original clean state (HEAD `854299c`, no working-tree
changes).

**One-line verdict:** The spine is unusually well reality-checked — every version is correct,
the two flagged `[ASSUMPTION]`s are the *right* things to flag and both turn out to be TRUE,
and the reviewer's hypothesis that "Hextra defaults to docs mode so cascade is unnecessary"
is **wrong** — the cascade is in fact *required*. Only minor omissions (two missing
`hugo.toml` params) keep this from a clean pass.

---

## Part 1 — Version / config claims

### Claim 1 — Hextra v0.12.3, Hugo 0.163.3, Go 1.26.4 / go directive 1.25.3 — **CONFIRMED (all five)**

| Claimed | On disk | Verdict |
| --- | --- | --- |
| Hextra `v0.12.3` locked in `go.mod` | `go.mod`: `require github.com/imfing/hextra v0.12.3 // indirect`; `go.sum` pins the same hash | **CONFIRMED** |
| Hugo `HUGO_VERSION=0.163.3` in `amplify.yml` | `amplify.yml` env: `HUGO_VERSION: 0.163.3` (extended) | **CONFIRMED** |
| Go toolchain `1.26.4` in build | `amplify.yml` env: `GO_VERSION: 1.26.4` | **CONFIRMED** |
| `go 1.25.3` directive | `go.mod`: `go 1.25.3` | **CONFIRMED** |
| "Handbook adds no dependency / no version drift" (AD-10 inherited) | Confirmed — the docs-mode layout, sidebar, TOC, last-updated, pager, and FlexSearch are all already in the locked Hextra module; the Handbook adds only content + project overrides | **CONFIRMED** |

Bonus reality-check (not claimed, but worth noting it holds): Hextra v0.12.3's own
`hugo.toml` declares `[module.hugoVersion] min = '0.146.0'`. Hugo 0.163.3 clears that floor,
and the amplify comment `≥ 0.146.0 Hextra v0.12.3 floor` is accurate.

### Claim 2 — Search is FlexSearch, `index=content`, `maxSectionResults=1`, "one result per topic" — **CONFIRMED**

`hugo.toml` on disk:
```toml
[params.search]
enable = true
type = "flexsearch"
[params.search.flexsearch]
index = "content"
maxSectionResults = 1
```
All three values match exactly. **CONFIRMED** as inherited config.

Does it genuinely yield "one result per topic" for **docs** pages (FR-7)? **YES.** I read the
mechanism in the locked module (`assets/js/flexsearch.js`, lines ~323–360). The search runs a
**page index** (one entry per content bundle = one per topic) and, *per matching page*, runs a
**section index** capped at `maxSectionResults`. With `maxSectionResults=1`, each matching
topic contributes at most one section row → effectively one result per topic. The mechanism is
**page-type-agnostic** — it behaves identically for docs pages and blog posts. The parent set
this for blog posts; it carries to the Handbook unchanged with the same effect. The spine's
convention-table note ("`maxSectionResults=1` already yields one result per topic … no
Handbook-specific tuning") is **accurate** — the `[ASSUMPTION] (confirm)` tag on it can be
dropped.

### Claim 3 — Existing shortcode convention `ckbutton`/`ckbuttons` (AD-17 is "on-pattern") — **CONFIRMED**

`layouts/shortcodes/` on disk contains exactly `ckbutton.html` and `ckbuttons.html` — project
shortcodes, brand-prefixed (`ck-`), documented with usage headers, rendering `.ck-*` classes
that resolve against `custom.css` tokens. A `go-deeper.html` project shortcode is squarely
on-pattern. **CONFIRMED.**

### Claim 4 — `custom.css` already defines `--ck-chip-{violet,fuchsia,amber}-text` AND light-mode AA-safe accent redefinitions — **CONFIRMED**

- `:root` (dark): `--ck-chip-violet-text: #c9b8ff`, `--ck-chip-fuchsia-text: #f3a8ff`,
  `--ck-chip-amber-text: #ffd591` (custom.css lines 36–38), plus matching `*-border` tokens.
- `html:not(.dark)` (light) block (lines 1073–1101) **redefines** each chip-text token to an
  AA-darkened value (`#6d28d9 / #a21caf / #92400e`, annotated "5.8–6.5:1 on the light
  surface"), and redefines `--ck-accent-text` (`#a21caf`, 6.3:1) and `--ck-accent-amber-text`
  (`#92400e`, 9.5:1).

So the spine's claim — Go Deeper group labels reuse **existing**, **already-AA-paired** tokens
(AD-17), and the UX "theme-aware accent" requirement is **largely already solved** — is
**CONFIRMED**. *Caveat (the spine already flags this correctly in Deferred):* the tokens are
AA-paired, but the *specific* Go Deeper label + stub-badge compositions have not been
contrast-checked on Hextra's light surfaces. The spine's "Light-mode AA re-verification"
deferral is the right call — not a launch gate, but a real follow-up.

### Claim 5 — Existing `[[menu.main]]` weights so "EM Handbook" slots in cleanly — **CONFIRMED**

`hugo.toml` weights: Posts 10, About 20, Search 30, Theme 40, GitHub 50, LinkedIn 60. The link
items (Posts/About) are 10/20 and the controls (search/theme/icons) are 30–60. An "EM
Handbook" link entry slots in cleanly at **weight 25** (between About and Search) or **15**
(between Posts and About) without disturbing the controls. The spine's plan to "add a single
`[[menu.main]]` entry" is sound. **CONFIRMED.** *(Minor: the spine never states the weight to
use — see fixes.)*

---

## Part 2 — Assessment of the `[ASSUMPTION]` tags

### AD-14 — `cascade: {type: docs}` for per-section docs mode — **right thing to flag; ASSUMPTION is TRUE — VERIFIED BY BUILD**

This is the single most load-bearing call in the spine, and the reviewer's hypothesis
("Hextra defaults to docs mode already, making the cascade unnecessary or different") had to be
settled empirically. I did three things:

**(a) Read the layout-selection logic in the locked module.** Hextra v0.12.3 ships *top-level*
`layouts/single.html` and `layouts/list.html` (the default for any untyped page) AND
`layouts/docs/single.html` + `layouts/docs/list.html` (the `type=docs` layouts). The decisive
difference:

- **default `single.html`** calls `{{ partial "sidebar.html" (dict … "disableSidebar" true …) }}`
  → the sidebar is rendered **collapsed / placeholder only — no content tree**.
- **`docs/single.html`** calls `sidebar.html` with **no `disableSidebar`** → the **full docs
  sidebar tree** renders, plus breadcrumb, last-updated, and pager.

So an untyped page does **NOT** get the docs sidebar tree by default. **The reviewer's
"cascade may be unnecessary" hypothesis is REFUTED — the cascade (or an equivalent
type-setting) is required to get the sidebar tree.** (Confirming evidence in the *parent* site:
`layouts/posts/single.html` explicitly passes `disableSidebar=true` to suppress the very tree
the Handbook wants — i.e., Hextra would otherwise want to show it.)

**(b) Confirmed sidebar scoping.** `sidebar.html` line 6:
`{{- $navRoot := cond (eq site.Home.Type "docs") site.Home $context.FirstSection -}}`.
Because this site's home is **not** type=docs (it's the custom blog landing), the sidebar root
falls to `$context.FirstSection` — the current page's top-level section. **Therefore the
Handbook sidebar shows only the Handbook tree, never the whole site.** This is exactly the
behaviour AD-14 wants, and it is *contingent on the home not being docs-typed* — which holds.

**(c) Proved it with a real build.** I scaffolded `content/handbook/_index.md` with
`cascade: { type: docs }`, a `the-transition/_index.md` (weight 1), and a topic, then ran
`hugo --gc` against the locked Hextra v0.12.3 module. Results:
- The Handbook topic output contained `hextra-sidebar-container` with sidebar links to
  `/handbook/the-transition/` and `/handbook/the-transition/first-90-days/` — **the docs tree
  rendered**.
- The Handbook sidebar did **not** leak `/posts/...` tree links (only the header-menu `/posts/`
  link, which is global chrome — expected and harmless).
- `public/about/index.html` contained **zero** handbook tree links — **the rest of the site was
  untouched.** Posts pages still rendered with the suppressed (no-tree) sidebar.

**Verdict: AD-14's mechanism is CONFIRMED correct for Hextra v0.12.3.** The `[ASSUMPTION]` was
the right thing to flag *and* resolves TRUE. The spine should record this as verified ("build-
confirmed 2026-06-25") and drop the assumption tag. One refinement for the implementer: the
Structural Seed already hints at it ("`layouts/handbook/` OR `_default` override scoped via
`type=docs`") — note that the Start Here override (AD-19) must shadow `docs/list.html` (the
section-index list layout) for the section root, since with `type: docs` the `_index.md`
renders via the docs **list** layout, not single.

### AD-15 — `enableGitInfo` under AWS Amplify — **right thing to flag; ASSUMPTION is TRUE, with one missing config dependency**

Two sub-questions:

**(i) Does Hugo `enableGitInfo` produce a git-derived "last updated"?** **YES — proven by
build.** With `enableGitInfo = true` AND `[params] displayUpdatedDate = true`, a *committed*
Handbook topic rendered:
`Last updated on <time datetime="2026-06-25T06:15:36.000Z">June 25, 2026</time>` — the value
is the git commit timestamp, not front matter. The Hextra `last-updated.html` partial (read on
disk) reads `.Lastmod` (which Hugo populates from GitInfo when `enableGitInfo` is on) and is
**already invoked by `docs/single.html`** — so docs-mode Handbook pages get it for free.

> **Gap (MINOR but real):** `last-updated.html` gates on **`site.Params.displayUpdatedDate`**,
> which Hextra does **not** default to true. So `enableGitInfo = true` alone is **not
> sufficient** — `hugo.toml` must also set `[params] displayUpdatedDate = true`. The spine
> (AD-15 and the Structural Seed comment `+ enableGitInfo`) names only `enableGitInfo` and
> omits `displayUpdatedDate`. Without it the page builds fine but silently shows no
> "last updated" line — quietly missing the FR-4 requirement. **Add `displayUpdatedDate = true`
> to the AD-15 / hugo.toml plan.**

**(ii) Does it work under AWS Amplify's git clone?** **Likely YES, but I cannot prove it from
this repo — UNVERIFIABLE here, and the spine's "fallback is front-matter `lastmod`" hedge is
the correct safety net.** `enableGitInfo` requires real commit history (a non-shallow clone)
at build time. AWS Amplify's managed builds **do** check out the repo with git history
available, so this normally works — but two known failure modes exist: (a) shallow clones
(`--depth 1`) leave only the tip commit, so older pages get the tip's timestamp; (b) if a build
ever runs from an artifact/zip rather than a git checkout, `.GitInfo` is nil entirely. The
current `amplify.yml` does no explicit clone config (Amplify manages it), so the behaviour
depends on Amplify's defaults, which I can't observe from disk. The spine **correctly flags
this as an `[ASSUMPTION]` and specifies a fallback** (front-matter `lastmod`) — that is exactly
the right hedge. **Keep the assumption tag until verified on a real Amplify build log;** the
fallback means a wrong assumption degrades gracefully rather than breaking the build.

---

## Part 3 — Did it assert anything it should have verified?

The spine is honest about its uncertainty — the two highest-risk mechanism calls (AD-14,
AD-15) are both tagged `[ASSUMPTION]`, which is the correct instinct. A few smaller items were
asserted as fact and check out, plus two omissions:

| Item | Status |
| --- | --- |
| Versions (Hextra/Hugo/Go) asserted in Stack table | **All correct** — properly reality-checked, as the "reality-verified … on disk" note claims |
| `--ck-chip-*` tokens "already in custom.css, already AA-paired for light mode" (AD-17) — asserted as fact | **Correct** |
| `ckbutton`/`ckbuttons` existing convention (AD-17) — asserted as fact | **Correct** |
| FlexSearch "one result per topic" tagged `[ASSUMPTION] (confirm)` | **Over-cautious** — it's verifiably true; tag can be dropped |
| `displayUpdatedDate = true` config dependency for AD-15 | **MISSING** — asserted `enableGitInfo` is enough; it isn't |
| The `[[menu.main]]` weight value for "EM Handbook" | **MISSING** — entry is planned but no weight named (slot 15 or 25 is clean) |

No claim was found to be **asserted-but-false**. The only defects are the two **omitted config
keys**, both minor and both easy to fix in the same `hugo.toml` edit AD-14/AD-15 already
require.

---

## Findings (severity-ordered)

1. **[MINOR] AD-15 omits `displayUpdatedDate = true`.** `enableGitInfo` populates `.Lastmod`,
   but Hextra's `last-updated.html` partial renders nothing unless `[params]
   displayUpdatedDate = true` is also set. Build-confirmed.
   **Fix:** add `displayUpdatedDate = true` to the `[params]` block in `hugo.toml`, alongside
   `enableGitInfo = true`, in the AD-15 rule and the Structural Seed comment.

2. **[MINOR] AD-14 Start Here override must shadow the docs *list* layout, not single.**
   With `type: docs`, `content/handbook/_index.md` is a section index → renders via
   `docs/list.html`. The AD-19 custom layout must therefore override the list layout for the
   handbook section (e.g. `layouts/handbook/list.html` or a `type=docs`-scoped `_index.html`).
   **Fix:** state this explicitly in AD-19 / Structural Seed so the implementer doesn't try to
   override `single.html` and get a blank Start Here.

3. **[NIT] Name the "EM Handbook" menu weight.** Existing weights: Posts 10, About 20, controls
   30–60. **Fix:** specify weight **15** or **25** in AD-14 so the link lands between the
   nav links and before the controls.

4. **[NIT] Drop two now-resolved `[ASSUMPTION]` tags.** Both verified TRUE here:
   (a) AD-14 `cascade {type: docs}` — build-confirmed correct for Hextra v0.12.3;
   (b) the FlexSearch "one result per topic" convention-table note — verified from module JS.
   **Fix:** mark them "verified 2026-06-25 (build)" rather than leaving them as open
   assumptions. **Keep** the AD-15 *Amplify-clone* assumption tag (genuinely unverifiable from
   disk; fallback already specified).

---

## Per-claim verdict table

| # | Claim | Verdict |
| --- | --- | --- |
| 1a | Hextra v0.12.3 in go.mod | **CONFIRMED** |
| 1b | Hugo HUGO_VERSION=0.163.3 in amplify.yml | **CONFIRMED** |
| 1c | Go 1.26.4 build / `go 1.25.3` directive | **CONFIRMED** |
| 2 | FlexSearch, index=content, maxSectionResults=1; "one result per topic" for docs | **CONFIRMED** |
| 3 | ckbutton/ckbuttons existing shortcode convention (AD-17 on-pattern) | **CONFIRMED** |
| 4 | custom.css has `--ck-chip-*-text` tokens + light-mode AA accent redefinitions | **CONFIRMED** |
| 5 | Existing `[[menu.main]]` weights; "EM Handbook" slots in cleanly | **CONFIRMED** |
| AD-14 | `cascade {type: docs}` is the correct per-section docs-mode lever (cascade is REQUIRED, not unnecessary; sidebar is section-scoped) | **CONFIRMED (build-verified); reviewer's "unnecessary" hypothesis REFUTED** |
| AD-15a | `enableGitInfo` yields git-derived last-updated (needs also `displayUpdatedDate=true`) | **CONFIRMED with caveat** |
| AD-15b | `enableGitInfo` works under AWS Amplify's git clone | **UNVERIFIABLE from disk (likely true; fallback correctly specified)** |
