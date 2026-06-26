---
title: "Engineering Manager Handbook — Author Build Cheat-Sheet"
audience: Carrie (author)
companion-to: ARCHITECTURE-SPINE.md
status: final
created: '2026-06-25'
---

# Engineering Manager Handbook — Author Cheat-Sheet

The one-page "how do I add a topic" reference. Everything here is the spine's rules made concrete. Work in the **`carriekroutil-site`** repo — that's where published content lives ([AD-20](ARCHITECTURE-SPINE.md)). This repo (`eng-mgr-toolkit`) is planning + raw wiki source only.

Publishing is unchanged from the rest of the site: **write markdown → `git push` → Amplify builds → live.** No CMS, ever.

---

## One-time setup (do once, before the first topic)

These are the only site-config changes the Handbook needs. All in `carriekroutil-site`.

**1. `hugo.toml` — add the nav entry:**

```toml
# Header nav — slots between Posts (10) and About (20)
[[menu.main]]
name = "Handbook"
url = "/handbook/"
weight = 15
```

("Last updated" is an authored `lastUpdated` front-matter field on each topic — no `hugo.toml` change needed.)

**2. `content/handbook/_index.md` — turn the section into docs mode + make it Start Here:**

```yaml
---
title: "Engineering Manager Handbook"   # page H1 / <title> — full identity (nav label stays "Handbook")
cascade:
  type: docs        # REQUIRED — without this, no sidebar tree (build-verified)
---
<!-- Start Here landing: guided-path step cards + section grid.
     Rendered by layouts/docs/list.html (root-guarded; the docs type slot — NOT
     layouts/handbook/list.html, which never runs under cascade type:docs). -->
```

**3. Create the 12 Section folders** with the *exact* slugs below — never re-slug ([AD-21](ARCHITECTURE-SPINE.md)). Each gets an `_index.md` with a unique `weight`:

| # | Section | Folder slug |
|---|---------|-------------|
| 1 | The Transition | `the-transition` |
| 2 | Managing Yourself | `managing-yourself` |
| 3 | People & Leadership | `people-leadership` |
| 4 | Performance & Growth | `performance-growth` |
| 5 | Hiring & Team Building | `hiring-team-building` |
| 6 | Team Health & Operations | `team-health-operations` |
| 7 | Delivery & Execution | `delivery-execution` |
| 8 | Operational Excellence | `operational-excellence` |
| 9 | Tools & Productivity | `tools-productivity` |
| 10 | Building with AI | `building-with-ai` |
| 11 | Security & Governance | `security-governance` |
| 12 | Engineering Foundations | `engineering-foundations` |

A Section `_index.md`:

```yaml
---
title: "The Transition"
weight: 1            # 1..12, matches the table; unique
---
Optional one-paragraph section intro.
```

---

## Adding a topic (the 90%-case recipe)

Create `content/handbook/{section-slug}/{topic-slug}.md` — a **flat file**. (Only use a `{topic}/index.md` folder if the topic ships its own images; then every image needs alt text or the build fails.)

```yaml
---
title: "The Engineer/Manager Pendulum"
weight: 20                     # order within the section; UNIQUE per section (no ties)
description: "When to swing back to hands-on, and how to do it without undermining your team."
lastUpdated: 2026-06-25        # authored; bump it when you meaningfully change the page
goDeeper:
  - group: Books               # Books · Courses · Tools  (only these three)
    title: "The Manager's Path"
    url: "https://www.oreilly.com/library/view/the-managers-path/9781491973882/"
    why: "the field guide for the IC→manager swing; chapters 4–5 are the pendulum."
  - group: Tools
    title: "Camille Fournier's blog"
    url: "https://www.elidedbranches.com/"
    why: "real-world write-ups when you're mid-swing and second-guessing."
---

A short framing intro: what this is and why it matters.

## A heading

Body. Long topics stay one page — the right-hand "on this page" TOC handles jumps.
Author H2/H3 well; that's what the TOC harvests.
```

**Internal links** to other topics — use `relref`, never a hand-typed path ([AD-22](ARCHITECTURE-SPINE.md); a bad target fails the build instead of 404-ing live). The path is **content-root-absolute**, so it includes the `/handbook/` prefix — a bare `section/topic.md` resolves against `content/` (not `content/handbook/`) and fails the build with `REF_NOT_FOUND`:

```markdown
See [first 90 days]({{< relref "/handbook/the-transition/first-90-days.md" >}}).
```

---

## Shipping a stub (a topic that's live but not yet written)

Same file, just `stub: true` and honest microcopy. **Never use `draft: true`** — that hides it from the build, nav, and search, breaking the "complete IA, nothing 404s" promise ([AD-18](ARCHITECTURE-SPINE.md)).

```yaml
---
title: "On-Call"
weight: 10
description: "Setting up humane on-call: rotations, escalation, and not burning people out."
lastUpdated: 2026-06-25
stub: true                     # → renders the inline "🚧 Expanding" badge by the title
goDeeper:
  - group: Books
    title: "Seeking SRE (ed. David Blank-Edelman)"
    url: "https://www.oreilly.com/library/view/seeking-sre/9781491978856/"
    why: "the on-call chapters are the realest thing written on humane rotations."
---

🚧 **Expanding** — the deep-dive's still cooking. Meanwhile, the Go Deeper picks
below are the real ones I'd hand you.
```

Notes:
- The **badge** appears automatically from `stub: true` (by the title). Your intro is *separate* warm microcopy — don't just re-type the badge.
- A stub still needs a `goDeeper` with **≥ 1 item** (see the hard rule below).
- Flip `stub: false` when you've written the body. That's the whole "expand a stub" loop.

---

## The rules the build enforces for you (it fails the build, not silently)

- **Every topic needs ≥ 1 `goDeeper` item** — including stubs. An empty/absent `goDeeper` fails the build ([AD-17](ARCHITECTURE-SPINE.md)). (Mirrors how the site already build-fails on a missing image alt.)
- **`goDeeper` groups are exactly `Books` · `Courses` · `Tools`** — a group only shows when it has items; no other group names.
- **`relref` targets must exist** — a broken internal link fails the build ([AD-22](ARCHITECTURE-SPINE.md)).
- **Weights are unique within a section** — two topics at `weight: 20` order unpredictably ([AD-16](ARCHITECTURE-SPINE.md)).
- **Images need alt text** — inherited site rule (AD-9).

## Preview before you push

```bash
hugo server     # local preview at localhost:1313/handbook/ — the only pre-publish check
```

Brand/styling is automatic — the Handbook inherits the site's theme and tokens; **don't add new colors** ([AD-17/FR-11](ARCHITECTURE-SPINE.md)). External Go Deeper links open in a new tab automatically.

---

*Full rationale and the complete invariant set: [`ARCHITECTURE-SPINE.md`](ARCHITECTURE-SPINE.md).*
