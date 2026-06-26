---
title: "Input Reconciliation — Brief & Addendum vs PRD"
status: review
created: 2026-06-24
updated: 2026-06-24
---

# Input Reconciliation: Brief + Addendum → PRD

Pass scope: did the PRD silently drop or under-represent meaningful intent from the source brief and addendum? Focus on qualitative intent the FR structure tends to lose.

## Verdict

The PRD preserves most source intent well — voice/tone, the curated-not-link-dump promise, the three-pillar differentiator, the bootcamp gap, phased launch, and stub-with-Go-Deeper are all carried through (often verbatim) in the Vision, Aesthetic & Tone, JTBD, Non-Goals, and Counter-metrics sections. A handful of meaningful items are **under-represented or dropped**, and one is a **soft contradiction**. None are fatal, but several should be folded back in before downstream UX/architecture/epics work.

## Gaps & Under-representations

### G1. Wiki→Section migration mapping is referenced but not carried (MEDIUM-HIGH)
The addendum holds a concrete 13-row table mapping each wiki page (by ID, word count, substantial/stub status) to target Section(s), including the cross-cutting splits (009 Tips & Tricks → Tools + Managing Yourself + Delivery; 003 Inclusive Teams → People & Leadership + Hiring; 012 Dev Lifecycle → Team Health + Delivery). The PRD (FR-9/FR-10) only says "per the addendum mapping" and "14 wiki pages." Two concrete facts get lost by pointer-only reference:
- **Source word counts / substantial-vs-stub status per page** — which pages are launch-quality content vs. which were already stubs (007 Team Health 59w, 011 Ceremonies 23w, 014 Security 119w, 001 Leadership 288w). The PRD's `[NOTE FOR PM]` in §6.2 names a *different* "substantive" set (Technical Upskilling, Coaching, Inclusive Teams, Dev Lifecycle, Tips) than the addendum's full substantial list — fine, but the authoritative per-page status only exists in the addendum.
- **Page 010 numbering gap** and **page 013 = 5116w (largest)** drive the "very large pages" Open Question (FR-4 TOC / OQ-2) — the PRD discusses large-page handling abstractly without anchoring it to 013.
- Recommendation: keep the table in the addendum (correct home) but ensure the PRD's migration FRs explicitly defer to it as the binding source AND that downstream epics pull the per-page status from it, not re-derive it. The mismatch between §6.2's list and the addendum's list should be reconciled.

### G2. Compounding-personal-brand framing is thinned (MEDIUM)
Brief differentiator #4 ("A named practitioner's compounding site") and the brief Vision ("compounding asset… naturally seeds other artifacts — talks, posts, coaching curricula") carry a strategic why: hosting on carriekroutil.com *compounds the brand instead of diffusing it*, citing the Larson/Orosz/Fournier reputation playbook. The PRD keeps brand-consistency as an FR (FR-11) and "something I'm proud to share" in JTBD, but the *compounding / reputation-playbook* rationale — why it must live under her own name rather than be a standalone product — is reduced to a single Non-User bullet ("personal-brand-anchored"). The forward-looking "seeds talks/posts/coaching curricula" vision is dropped entirely. This is the load-bearing answer to "why not a standalone site?" and should survive into the PRD Vision.

### G3. Three-pillar differentiator present but the "dual-mode UX as structural moat" framing is softened (LOW-MEDIUM)
The three legs (people + tooling + foundations) are well-preserved (Vision §1, Aesthetic & Tone). But brief differentiator #3 explicitly frames **dual-mode UX (learning path + quick reference) as "the structural moat… the structure is the product."** The PRD implements both modes (FR-3 + FR-7/sidebar) but never states that the *dual-mode structure itself is a differentiator/moat* — it reads as a feature, not the strategic edge. Worth a sentence in Vision so UX doesn't treat Start Here as optional polish.

### G4. Security & Governance scope guard-rail dropped (LOW-MEDIUM)
Addendum Section 11 carries an explicit negative scope note: Security & Governance = OWASP/NIST/SOC 2/ISO 27001/GDPR compliance literacy, **"Not 'org structures.'"** Plus the brief's debugging "hard-knocks reps / aha moment" nuance under Foundations. The PRD lists the 12 Sections by name only; the *contents and the explicit "not org structures" guard-rail* live solely in the addendum. Low risk if addendum is treated as binding for IA seeds, but the guard-rail is the kind of intent an epic author can miss.

### G5. "Equal parts heart and hands-on craft" + specific seed examples (LOW)
Voice is well-captured. Minor: the brief's concrete starter-content examples (first-90-days, boundaries & burnout, 1:1s & reviews, scope/resources/time levers, on-call, i18n/a11y, AI-assisted/native) and the vivid "Simon Sinek, a git cheat sheet, and a plain-English i18n intro under one roof" image are reduced in the PRD. The Sinek/git image is a strong differentiator illustration worth one line; the seed list is adequately covered by addendum + §6.

## Contradiction

### C1. Tags taxonomy: brand constraint says lowercase `tags` taxonomy exists; PRD defers/retires tags (SOFT CONTRADICTION — reconcile)
Addendum "Technical & brand constraints" lists **"lowercase `tags` taxonomy"** as an inherited site brand constraint. The PRD deliberately **defers/retires tags** for the Handbook (FR-8 retired, §6.2, Deferred note), arguing tags are a blog convention not a docs convention. These aren't strictly contradictory — the site-wide `tags` taxonomy can exist for the blog while the Handbook docs section simply doesn't use it — but as written they *appear* to conflict and an implementer could read it either way. The PRD should state explicitly: "the site's existing `tags` taxonomy remains for blog content; Handbook Topics do not use tags." Otherwise downstream may either wire tags into Handbook front matter (per addendum) or assume the taxonomy was removed (per PRD).

## Well-Preserved (no action)
- Voice/tone: practical, warm, direct, opinionated, first-person/"Pro Tip" — fully carried (Aesthetic & Tone), incl. anti-references (awesome-list, corporate tone, walls of text).
- Curated-with-judgment-not-link-dump: carried in Vision, FR-5 intent, SM-C2, anti-references.
- Bootcamp/self-taught foundations gap: carried (JTBD §2.1 bullet 4, Vision "must-knows", FR-10 i18n/a11y/video).
- Phased launch / complete-IA-day-one / nothing-404s: carried (FR-6, FR-10, MVP).
- Coachee-next-week constraint: carried strongly (UJ-2 Jordan, SM-2, Target User).
- Tech/brand constraints (Hugo Module, Amplify, GoatCounter, single repo/pipeline): carried (FR-11/12/13, NFRs) and correctly delegated to addendum/architecture.
