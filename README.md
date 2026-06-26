# Engineering Manager Handbook — the BMAD build archive

This is the planning-and-process trail behind my **[Engineering Manager Handbook](https://carriekroutil.com/handbook/)** — the curated guide to engineering leadership that now lives on my site. For years it was a sprawling, hard-to-navigate GitHub wiki. This repo is the record of how I used the **BMAD Method** — a spec-first, role-based way of working with an AI coding agent — to reshape those years of notes into a real handbook (branded, navigable, searchable) and ship it to **carriekroutil.com/handbook**.

The handbook itself — the Hugo + [Hextra](https://github.com/imfing/hextra) site — lives in my `carriekroutil-site` repo. **This** repo is the thinking that produced it: the brief, the requirements, the design, the architecture, the epics and stories, the reviews, and the retrospective. I'm keeping it public and archived so future me can remember how it came together — and so anyone curious about building in public with BMAD can learn from the whole paper trail, not just the result.

## What shipped

- **Live:** https://carriekroutil.com/handbook/
- **53 topics** across **12 sections** — 23 migrated from the old wiki, 30 honest "🚧 Expanding" stubs that fill in over time
- A guided **"Start Here"** front door, full-text search, dark mode, WCAG 2.1 AA — on the same stack as the rest of the site (no new infrastructure, no new dependencies)
- Built across **3 epics** and shipped in a single go-live merge

## How it was built (the BMAD flow)

BMAD front-loads the thinking: you break a project into planning artifacts *before* any code gets written, moving through a series of roles. Mine went:

1. **Analysis** → a product **brief** (what this is, who it's for, why)
2. **Planning** → a **PRD** (requirements) and a **UX** design (experience + visual)
3. **Solutioning** → an **architecture** spine, the **epics & stories** breakdown, and an **implementation-readiness** check
4. **Implementation** → per epic: sprint planning → create story → dev story → code review, then a **retrospective**

A note on execution style, because it shows up in the files below: **Epics 1 & 2 I ran autonomously** (straight through to a pull request), while **Epic 3 — the front door and go-live — I ran story-by-story**, reviewing each step myself. That's why Epics 1 and 3 have individual story files here and Epic 2 doesn't: Epic 2's 19 stories are fully specified in `epics.md` (Story 2.1–2.19) and proven by the shipped content, but the autonomous run didn't leave 19 separate story files behind. Both modes worked; they just leave different paper trails.

## What's in `_bmad-output/`

### `planning-artifacts/` — the thinking

- **`briefs/.../brief.md`** — the product brief: vision, the problem (knowledge split + scattered, foundations assumed-not-taught), the dual-mode solution, scope. **`addendum.md`** maps the old wiki pages into the new information architecture.
- **`prds/.../prd.md`** — the PRD: functional/non-functional requirements, user journeys, success criteria. (`reconcile-brief.md`, `review-rubric.md` — reconciliation + quality pass.)
- **`ux-designs/.../`** — **`EXPERIENCE.md`** (information architecture, surfaces, key flows, voice & tone) and **`DESIGN.md`** (visual design + tokens, inheriting the site's brand), plus clickable **`mockups/`** (`start-here.html`, `topic.html`) and a completeness review.
- **`architecture/.../`** — **`ARCHITECTURE-SPINE.md`** (the invariants / architecture decisions that govern the whole build) and **`BUILD-CHEATSHEET.md`** (the one-page "how do I add a topic" reference), plus adversarial/verification `reviews/`.
- **`epics.md`** — the full epics & stories breakdown: all 3 epics, every story written as acceptance criteria.
- **`implementation-readiness-report-...md`** — the pre-build go/no-go check across PRD, UX, architecture, and epics.

### `implementation-artifacts/` — the building

- **`sprint-status.yaml`** — the tracker: every story's status (all `done`) and the retro action items.
- **`1-1` … `1-6`** — Epic 1 story files (the branded, navigable shell): docs-mode section + nav, the 12 sections, brand-match, search, the accessibility floor, pipeline-ready.
- **`3-1` … `3-4`** — Epic 3 story files: the Start Here landing, the UAT fix loop, the go-live merge, and post-launch analytics/discoverability verification.
- **`epic-3-retro-...md`** — the retrospective: what went well, the lessons, and action items.
- *(Epic 2's stories live in `epics.md` — see the execution-style note above.)*

## For future me (and anyone reading)

The retrospective (`epic-3-retro-...md`) is the most honest part. The lesson I'd carry forward: **bake responsive/mobile-size testing into acceptance criteria and code review** — the code review was strong on logic and accessibility but never exercised actual screen sizes, and a mobile navigation bug only surfaced because I'd built in a manual UAT step. Trust the spec, but verify the framework's real behavior; and write decisions down where they'll outlive the conversation.

---

*Built in public with the BMAD Method and Claude Code. The handbook lives at [carriekroutil.com/handbook](https://carriekroutil.com/handbook/).*
