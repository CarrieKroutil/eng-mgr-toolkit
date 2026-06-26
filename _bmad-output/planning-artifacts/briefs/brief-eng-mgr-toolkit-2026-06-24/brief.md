---
title: "Product Brief: Engineering Manager Handbook"
status: complete
created: 2026-06-24
updated: 2026-06-24
---

# Product Brief: Engineering Manager Handbook

## Executive Summary

The Engineering Manager Handbook is an opinionated, practitioner-authored guide to engineering leadership, published as a **Documentation section of [carriekroutil.com](https://carriekroutil.com)**. It takes years of Carrie's collected leadership wisdom — today trapped in a hard-to-navigate GitHub wiki — and reshapes it into a handbook that works two ways: a **guided "Start Here" path** for new and aspiring managers, and a **fast topic reference** for busy leaders mid-problem.

Its angle is what the field largely lacks: a single, opinionated voice that **blends three things most resources keep apart — people leadership, hands-on tooling, and engineering foundations (the technical "must-knows")** — Simon Sinek, a `git` cheat sheet, and a plain-English intro to internationalization or on-call monitoring under one roof, all **curated with judgment** rather than dumped as links. It is built on the same Hugo + Hextra + AWS Amplify stack as the rest of carriekroutil.com, so it inherits the brand, search, and infrastructure already in place.

It ships next week with its full structure live — real content where it exists, honest "coming soon" stubs where it doesn't — and expands in place from there.

## The Problem

Engineering-management knowledge online is **split and scattered**. Resources pick a lane: people-leadership (The Manager's Path, Rands, Plato) *or* engineering craft and industry intelligence (The Pragmatic Engineer, StaffEng). The best ongoing material sits behind paywalls; the free tier is fragmented across blogs, subreddits, and Slack threads.

What passes for curation is **unopinionated link-dumps** — "awesome-list" GitHub READMEs that optimize for coverage, not judgment: undated, unranked, no path through them.

That leaves two people underserved:
- **The new manager** (Carrie's coachee is the concrete example) has no guided path — just a firehose of disconnected articles and no sense of what to read first.
- **The busy experienced leader** — including Carrie herself — has no fast, trustworthy place to re-find the one tactic or framework they remember but can't relocate.

And there's a quieter gap underneath all of it: **engineering foundations are assumed, not taught.** Many capable engineers — especially those who came up through bootcamps or self-taught paths — never got grounding in must-knows like internationalization (i18n), accessibility (a11y), video localization (captions *and* spoken-word), or standing up on-call monitoring and alerting. They also missed the hard-knocks reps: the hours of debugging that end in the *aha* moment that actually makes a lesson stick. A handbook can't hand someone those reps, but it can name the concepts, demystify what nobody told them they were supposed to know, and point to where to go learn it for real.

Carrie already *has* the raw material, accumulated over years. But it lives in a GitHub wiki: flat, hard to navigate, no search, off-brand, and not somewhere she'd proudly point a colleague.

## The Solution

A **Handbook section integrated into carriekroutil.com**, using Hextra's documentation mode. Two ways in, one source of truth:

- **Start Here (the landing page):** the `/handbook` home *is* the guided path — a curated, ordered route through the fundamentals for new/aspiring EMs ("read these in order and you'll be oriented"). It's the entry *experience*, not a sidebar section, so newcomers get a path while everyone else drops straight into the sidebar or search.
- **Topic Reference:** the full handbook as a searchable, sidebar-navigable tree of 12 sections — The Transition (Engineer → Manager); Managing Yourself; People & Leadership; Performance & Growth; Hiring & Team Building; Team Health & Operations; Delivery & Execution; Operational Excellence; Tools & Productivity; Building with AI; Security & Governance; and Engineering Foundations last (the deepest) — built for fast lookup.

The existing wiki content is **restructured and migrated** into this IA. Content gaps (today's stub pages, plus any new sections the restructure introduces) launch as **lightweight, upfront stubs** so navigation is complete from day one, then fill in over time. Full-text search, dark-mode, copy-able code, callouts, and diagrams come for free from Hextra — the "interactive" experience without a custom app to build or maintain.

**A consistent "Go Deeper" pattern.** Every topic ends the same way — a short, curated set of next steps: book suggestions, courses, and upskilling tools to continue the journey. It's the curation-with-judgment promise made concrete, and it serves both audiences at once: a clear path forward for newcomers, a vetted shortlist for the experienced. It also gives every stub page something useful to offer on day one, before its body is fully written.

## What Makes This Different

Honest about the moat: the content itself isn't proprietary — much of it curates public resources. The edge is **voice, judgment, and structure**, not secret knowledge:

1. **People + tooling + foundations in one voice.** Almost no EM resource blends leadership guidance, hands-on tooling, *and* plain-English engineering foundations under a single voice. That three-legged blend is the genuine differentiator — and it demystifies the must-knows that bootcamp and self-taught paths often skip.
2. **Opinionated curation, not a link dump.** Carrie's lived experience decides what's worth your time and why.
3. **Dual-mode UX as the structural moat.** Nothing in the landscape serves *both* a sequential learning path and a quick-reference lookup in one place. The structure is the product.
4. **A named practitioner's compounding site.** The proven reputation playbook (Larson, Orosz, Fournier): a consistent personal site under your own name. Hosting this on carriekroutil.com compounds the brand instead of diffusing it.

## Who This Serves

- **Carrie (primary, "user zero").** Her own quick reference — the place she goes to re-find a framework or tactic. If it's not genuinely useful to her, it's not done.
- **New / aspiring engineering managers (primary).** The manager she's actively coaching is the first real user, next week. They need orientation and a path, not a firehose.
- **Experienced engineering leaders (secondary).** Want a refresher or a specific tactic fast; enter via search or the reference tree, not the learning path.
- **The broader public (secondary).** People who discover it through carriekroutil.com — it doubles as a portfolio piece and credibility signal. [ASSUMPTION] Public reach is a welcome bonus, not a primary driver.

## Success Criteria

Right-sized to a passion project with real practical use — not vanity metrics:

- **It's Carrie's actual go-to.** She reaches for it instead of digging through bookmarks or the old wiki. (The honest tell: is the wiki retired?)
- **The coachee finds it useful next week.** They can be pointed to it and get real value — orientation via the path, answers via search. [ASSUMPTION] Light qualitative signal (did they use it, did it help) over hard metrics.
- **It's live on carriekroutil.com and Carrie's proud to share it.** Brand-consistent, coherent structure, nothing embarrassing in the nav.
- **Leading indicators (later, low-effort via GoatCounter):** return visits, which topics get traffic, search usage — signals of what to expand next. [ASSUMPTION] Pageview *growth* is not a v1 goal.

## Scope

**In (v1, ships next week):**
- Integrate a `/handbook` Documentation section into carriekroutil.com (Hextra docs mode), brand-matched.
- Dual-mode IA: a **Start Here landing-page** guided path + a searchable 12-section topic reference.
- Restructure and migrate the existing 14 wiki pages into the new IA; honest "coming soon" stubs fill all gaps so the full tree is navigable day one.
- A Handbook entry point in the site's primary navigation.
- Seed all 12 sections (stubs + "Go Deeper" acceptable), with starter content where it exists — e.g. first-90-days (Transition), boundaries & burnout (Managing Yourself), 1:1s & reviews (Performance & Growth), prioritization & the scope/resources/time levers (Delivery & Execution), on-call & incident response (Operational Excellence), i18n/a11y/video localization (Foundations), AI-assisted & AI-native engineering (Building with AI) — then expand in place over time. *(Full section map in the addendum.)*
- A consistent per-topic **"Go Deeper"** block (curated book suggestions, courses, and upskilling tools) as a content-model convention across the handbook.

**Out (deferred or non-goals):**
- A custom/bespoke "interactive" web app — Hextra docs mode covers the interactivity that matters.
- A separate repo, domain, or deploy pipeline — it lives in carriekroutil-site.
- Accounts, personalization, comments, ratings, or community features.
- Gated/paid content or a newsletter.
- Exhaustively filling every stub before launch — content expands in place after go-live.
- [ASSUMPTION] Interactive tools/calculators (e.g. a "levers" widget) — nice future ideas, not v1.

## Vision

Over time, the go-to opinionated engineering-management handbook under Carrie's brand — a compounding asset that grows with her experience and the people she coaches. Naturally seeds other artifacts (talks, posts, coaching curricula) and becomes the link she sends whenever someone steps into engineering leadership. It stays honest to its roots: practical, in her voice, equal parts heart and hands-on craft.
