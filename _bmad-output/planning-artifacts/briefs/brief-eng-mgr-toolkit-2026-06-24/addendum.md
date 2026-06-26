---
title: "Addendum — Engineering Manager Handbook"
status: draft
created: 2026-06-24
updated: 2026-06-25
---

# Addendum — Engineering Manager Handbook

Detail captured during the brief conversation that belongs to **downstream documents** (PRD, UX, Architecture) rather than the lean brief itself. Decisions and their rationale live in `.memlog.md`; this file holds the working depth.

## Information Architecture (full)

**Start Here** — the `/handbook` landing page *is* the guided path (an ordered route for newcomers). Not a sidebar section. Everyone else enters via the sidebar tree or search.

**12 sidebar sections, in order** (grouping/nesting to be refined in UX):

| # | Section | Topic seeds |
|---|---------|-------------|
| 1 | The Transition (Engineer ⇄ Manager) | IC→manager identity shift, the Engineer/Manager Pendulum (both directions), "is this the right move," first 90 days, impostor syndrome, **going back to IC (the swing back)** |
| 2 | Managing Yourself | Time, energy, boundaries, burnout, your own growth as a leader, building your leadership network (peers, mentors, sponsors, communities) |
| 3 | People & Leadership | Leadership responsibilities, inclusive teams, belonging, communication, strategic thinking; managing-up & cross-functional as sub-topics |
| 4 | Performance & Growth | 1:1s, feedback, performance reviews, underperformers, promotions, career ladders, coaching |
| 5 | Hiring & Team Building | Recruiting, inclusive interviewing, onboarding, building a team |
| 6 | Team Health & Operations | Team health, ceremonies, development lifecycle |
| 7 | Delivery & Execution | Prioritization, roadmaps, estimation, tech-debt vs. business impact, the scope/resources/time levers |
| 8 | Operational Excellence | On-call, monitoring, alerting, incident response |
| 9 | Tools & Productivity | Automation tools, tips & tricks |
| 10 | Building with AI | AI literacy, AI-assisted engineering, AI-native engineering, leading AI-adopting teams |
| 11 | Security & Governance | Security standards & the standards orgs an EM should know — OWASP (incl. Top 10), NIST — plus compliance literacy (SOC 2, ISO 27001, GDPR, CCPA & cookie compliance). *Not "org structures."* |
| 12 | Engineering Foundations | Technical upskilling — split into 5 topics: software architecture & design; delivery metrics & agile foundations; cloud-native & infrastructure; networking & protocols; a software-engineering learning path. Plus i18n, a11y, video localization (captions + spoken-word), debugging craft. Last because it's the deepest. |

**Per-topic "Go Deeper" convention:** every topic page ends with a curated block of book suggestions, courses, and upskilling tools. Standard content-model element across the handbook; gives stub pages day-one value.

## Existing wiki content → target section mapping

Source: `eng-mgr-toolkit.wiki` (14 pages, ~14.5k words). Migration targets:

| Wiki page | Target section(s) | Notes |
|-----------|-------------------|-------|
| 001 Leadership Responsibilities | People & Leadership | Stub (288w); seeds The Transition, Hiring, Performance |
| 002 Coaching Skills | Performance & Growth | Substantial |
| 003 Guide to Inclusive Teams | People & Leadership; Hiring (inclusive interviewing) | Largest people page (1454w) |
| 004 Create a Culture of Belonging | People & Leadership | Substantial |
| 005 Act & Think Strategically | People & Leadership | Substantial |
| 006 Communicate Effectively | People & Leadership | Substantial |
| 007 Team Health | Team Health & Operations | **Stub (59w)** |
| 008 Automation Tools | Tools & Productivity | Substantial |
| 009 Tips and Tricks | Tools & Productivity; Managing Yourself (brag list, email, time); Delivery (levers) | Substantial; spans sections |
| 011 Team Ceremonies | Team Health & Operations | **Stub (23w)** |
| 012 Development Lifecycle | Team Health & Operations; Delivery & Execution | Substantial (2001w) |
| 013 Technical Upskilling | Engineering Foundations (split into 5 topics: architecture & design; delivery metrics & agile; cloud-native & infra; networking & protocols; SWE learning path) | Largest page (5116w) |
| 014 Security Standards Orgs | Security & Governance | **Stub (119w)** — OWASP, NIST |

**Net-new sections (launch as stubs + Go Deeper):** The Transition; Managing Yourself; Hiring & Team Building; Delivery & Execution; Operational Excellence; Building with AI; plus new Foundations must-knows (i18n, a11y, video localization). Page 010 is a numbering gap (no content).

## Technical & brand constraints (for PRD / Architecture)

- **Publish target:** the existing `carriekroutil-site` repo; handbook lives under `content/handbook/` using Hextra's **docs** content type (left sidebar tree + FlexSearch). `eng-mgr-toolkit` (this repo) remains the BMAD planning + source-content home.
- **Theme:** Hextra consumed as a **version-locked Hugo Module** (`github.com/imfing/hextra` in `go.mod`) — never vendored or edited in place (existing site decisions AD-5/AD-10).
- **Build/deploy:** AWS Amplify (`amplify.yml`) installs pinned Hugo **extended** + Go, runs `hugo mod get`, builds `hugo --gc --minify`; single pipeline, `main` branch, production only. Route 53 domain.
- **Brand:** fuchsia primary; brand overrides in `assets/css/custom.css`; dark-default theme with toggle; lowercase `tags` taxonomy. Privacy-friendly **GoatCounter** analytics (production only).
- **Nav:** add a "Handbook" entry to the existing header menu (`[[menu.main]]` in `hugo.toml`).
- **Interactivity** is delivered by Hextra docs features (search, sidebar, code-copy, callouts, tabs, mermaid) — no custom app.

## Phased launch

Ship the **complete restructured IA** next week: migrated content where it exists, honest "coming soon" stubs (each with a real Go Deeper block) everywhere else, so navigation is whole and nothing 404s. Expand stub bodies in place, iteratively, post-launch. Immediate user: a new manager Carrie is coaching.

## Landscape positioning (research, 2026)

EM resources split into people-leadership *or* eng-craft; curation is unopinionated link-dump "awesome lists." Whitespace = opinionated curation + people/tooling/**foundations** blend + dual-mode UX (learning path + quick reference), authored by a named practitioner. Proven reputation playbook: a consistent personal site under your own name (Larson/lethain.com, Orosz, Fournier). Sources captured in research digest.
