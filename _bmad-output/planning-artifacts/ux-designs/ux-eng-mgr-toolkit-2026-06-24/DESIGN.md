---
status: final
created: 2026-06-24
updated: 2026-06-24
sources:
  - ../../prds/prd-eng-mgr-toolkit-2026-06-24/prd.md
  - ../../briefs/brief-eng-mgr-toolkit-2026-06-24/brief.md
inherits:
  - carriekroutil.com DESIGN.md (carriekroutil-site repo: _bmad-output/.../ux-carriekroutil-site-2026-06-22/DESIGN.md)
mockups:
  - ./mockups/start-here.html
  - ./mockups/topic.html
colors:
  # Inherited verbatim from carriekroutil.com — do not diverge.
  bg: "#0a0a0a"
  surface: "#15151a"
  surface-2: "#1c1c23"
  border: "#2a2a33"
  fg: "#ededed"
  muted: "#a0a0ab"
  accent-violet: "#8b5cf6"
  accent-fuchsia: "#d946ef"
  accent-amber: "#f59e0b"
  accent-gradient: "linear-gradient(90deg, #8b5cf6, #d946ef, #f59e0b)"
  link: "#d946ef"
  focus: "#d946ef"
  meta-min: "#a0a0ab"
  # Docs-mode additions:
  stub-badge-bg: "#1c1c23"
  stub-badge-text: "#ffd591"   # amber tint — "in progress", not alarming
  stub-badge-border: "#f59e0b"
  sidebar-active: "#d946ef"     # current topic marker = fuchsia link color
  # Theme-aware light-mode companions — the bright tints above fail AA on white; these pass on light surfaces:
  accent-violet-light: "#6d28d9"
  accent-fuchsia-light: "#a21caf"
  accent-amber-light: "#b45309"
typography:
  # Inherited; docs body uses the same reading scale.
  font-sans: "ui-sans-serif, system-ui, -apple-system, 'Segoe UI', Roboto, Helvetica, Arial, sans-serif"
  font-mono: "ui-monospace, SFMono-Regular, Menlo, Consolas, monospace"
  scale-h1: "2.0rem"          # topic titles a touch smaller than the blog hero (2.5rem)
  scale-h2: "1.35rem"
  scale-h3: "1.1rem"
  scale-body: "1.05rem"
  scale-small: "0.85rem"
  weight-body: 400
  weight-strong: 600
  weight-display: 800
rounded:
  card: "18px"
  control: "10px"
  pill: "999px"
spacing:
  base: "4px"
  gutter: "22px"
  reading-max: "760px"        # comfortable line length for long handbook prose
  sidebar-width: "300px"
focus:
  ring: "2px solid #d946ef"
  style: ":focus-visible"
components:
  # Inherited site components (brand-wordmark, header-nav, theme-toggle, search, footer-accent)
  # carry over unchanged. New docs-mode components:
  - docs-sidebar
  - on-this-page-toc
  - breadcrumbs
  - go-deeper-block
  - stub-badge
  - pro-tip-callout
  - path-step-card
  - section-grid-card
  - prev-next-nav
---

# Engineering Manager Handbook — Visual Identity

## Brand & Style

**This is the same site, a new wing.** The Handbook inherits carriekroutil.com's visual identity wholesale — the [existing `DESIGN.md`](../../briefs/brief-eng-mgr-toolkit-2026-06-24/addendum.md) is the parent. Same Hugo **Hextra** theme, same gradient wordmark and header/footer, dark-default with toggle, the violet→fuchsia→amber accent gradient as a sparing garnish, the cozy rounded surfaces. A reader moving from a blog post into the Handbook should not perceive a seam.

The one shift in *register*: the Handbook is **docs mode**, not blog mode — a persistent left sidebar tree and a right-hand "on this page" TOC replace the post-card grid. The feel stays warm and personal (it's a handbook, not enterprise documentation), but the layout optimizes for **navigation and lookup**. All brand overrides continue to live in the existing `assets/css/custom.css`; this doc records only the docs-mode delta.

## Colors

Inherited verbatim (see frontmatter; AA-verified on the parent site — body 16.9:1, muted 7.65:1, fuchsia link 5.71:1). Docs-mode additions:

- **Stub badge** — amber-tinted pill (`{colors.stub-badge-text}` `#ffd591` text, dimmed amber border) on `{colors.surface-2}`. Amber reads as "in progress / coming soon," not as an error. Never red.
- **Sidebar active item** — current topic marked with the fuchsia link color (`{colors.sidebar-active}`) + a left accent bar; ancestors bolded.
- **Theme-aware accent text (build requirement):** the bright tints for the stub badge and Go Deeper labels (`#ffd591` / `#c9b8ff` / `#f3a8ff`) pass AA on the dark surfaces but **fail on light-mode white** (amber on white ≈ 1.4:1). Ship them as theme-aware pairs — bright in dark mode, darker companions in light mode: `{colors.accent-violet-light}` `#6d28d9`, `{colors.accent-fuchsia-light}` `#a21caf`, `{colors.accent-amber-light}` `#b45309` (all AA on white). Verify in the light theme before launch.

## Typography

Inherited stack. Topic titles use `{typography.scale-h1}` `2.0rem` (slightly smaller than the blog's `2.5rem` hero, since handbook pages are reference, not landing). Body at `1.05rem`, line-height ~1.6. Headings within a topic (H2/H3) are what the auto-TOC harvests — author them well.

## Layout & Spacing

- **Docs layout:** left **sidebar** (`{spacing.sidebar-width}` ~300px, the 12-section tree) · centered **content column** (`{spacing.reading-max}` ~760px) · right **TOC** rail ("on this page", auto-generated). This is Hextra's docs three-column; we tune widths, not structure.
- **Start Here landing** breaks the three-column docs frame: a single centered column (sidebar still present) with the guided-path cards and a section grid.
- Mobile: sidebar collapses to Hextra's drawer; TOC collapses to a top "on this page" disclosure. Single column.

## Elevation & Depth

Inherited: flat and calm; depth from surface contrast + a subtle hover lift on cards. The **Go Deeper block** and **path-step cards** are `{colors.surface}` wells with `{rounded.card}` radius — a visible but quiet step up from the page.

## Shapes

Inherited: cards `{rounded.card}` 18px, controls 10px, the stub badge and any chips fully round (`{rounded.pill}`).

## Components

Visual reference: [Start Here](./mockups/start-here.html), [Topic page — full + stub states](./mockups/topic.html). Spines win on conflict with any mock. New docs-mode components (behavior in `EXPERIENCE.md`):

- **docs-sidebar** — the persistent left tree of 12 Sections → Topics; current item marked fuchsia + accent bar; sections collapsible.
- **on-this-page-toc** — right-rail TOC auto-built from a topic's H2/H3; scroll-synced (Hextra default).
- **breadcrumbs** — `Handbook / {Section} / {Topic}` above the title.
- **go-deeper-block** — a `{colors.surface}` card closing every topic: a "📚 Go Deeper" heading and curated items grouped **Books · Courses · Tools** (groups shown only when populated). Each item: title (link) + one-line "why." **Color:** group labels carry the three brand accents (Books = violet `#c9b8ff`, Courses = fuchsia `#f3a8ff`, Tools = amber `#ffd591`) echoing the topic-chip tints; item titles are body-color (`{colors.fg}`) links that turn fuchsia on hover — colorful-but-calm, never a wall of purple.
- **stub-badge** — the amber "🚧 Expanding" pill beside a stub topic's title; an **inline non-heading** element (never an `Hn`) so it doesn't disturb heading order or the auto-TOC. Text is theme-aware (see Colors).
- **pro-tip-callout** — Hextra callout (info/tip variant) for Carrie's first-person "Pro Tip" asides; tip accent uses the fuchsia/amber family, not generic blue.
- **path-step-card** — on Start Here: a numbered step in the guided path (number, Section/Topic name, one-line "why this," link).
- **section-grid-card** — on Start Here: a compact card per Section for "browse all 12."
- **prev-next-nav** — bottom-of-topic previous/next within the guided path or section (Hextra default).

**Focus & controls:** every interactive element is a real `<a>`/`<button>` with an accessible label and a visible focus ring (`{focus.ring}`, `:focus-visible`) — inherited site requirement.

## Do's and Don'ts

- **Do** make the Handbook visually continuous with the blog — same wordmark, header, gradient garnish, dark-default.
- **Don't** introduce a second visual language or a "docs portal" chrome that breaks the personal feel.
- **Do** keep the stub badge amber and calm — "expanding," never an error/red.
- **Don't** fill the Go Deeper block or path cards with the gradient; reserve the gradient for the wordmark/accents as on the parent site.
- **Do** let long topics breathe at `{spacing.reading-max}` with the auto-TOC for in-page jumps.
- **Don't** add heavy shadows or busy chrome — depth is surface contrast + gentle lift, as inherited.
