# Follow-up: contribute a `banner` landmark fix upstream to Hextra

> ⏰ **Reminder:** Carrie will do this later. **Raise it again at the Epic 1 retrospective** (registered as open action item `hextra-banner-landmark-upstream` in `sprint-status.yaml`).

**Status:** open · **Owner:** Carrie · **Target repo:** [imfing/hextra](https://github.com/imfing/hextra) (verified against `v0.12.3`)
**Found during:** Engineering Manager Handbook Epic 1 (Story 1.5, a11y floor) — carriekroutil-site.
**Goal:** Fix the gap upstream so carriekroutil.com *and all Hextra sites* get a `banner` landmark, instead of vendoring a local partial override.

## The gap

Hextra's navbar partial wraps the site header in a plain `<div>`, so rendered pages expose **no `banner` landmark**. Screen-reader users navigating by landmarks get `navigation` / `main` / `complementary` / `contentinfo`, but no "site header / banner" region.

Source: `layouts/_partials/navbar.html`, the outer `.hextra-nav-container` element:

```html
<div class="hextra-nav-container hx:sticky hx:top-0 hx:z-20 hx:w-full hx:bg-transparent hx:print:hidden">
  <div class="hextra-nav-container-blur ..."></div>
  <nav class="hextra-max-navbar-width ...">…</nav>
</div>
```

It's a body-level region, so it *should* be a `<header>` (which the browser maps to `role="banner"` at the top level). Not a WCAG 2.1 AA failure — a `banner` landmark is ARIA APG best practice — but a real, cheap a11y win that complements Hextra's existing skip-link + `<nav>`/`<main>`/`<aside>`/`<footer>`.

## The fix (one element, no visual/behavior change)

```diff
--- a/layouts/_partials/navbar.html
+++ b/layouts/_partials/navbar.html
-<div class="hextra-nav-container hx:sticky hx:top-0 hx:z-20 hx:w-full hx:bg-transparent hx:print:hidden">
+<header class="hextra-nav-container hx:sticky hx:top-0 hx:z-20 hx:w-full hx:bg-transparent hx:print:hidden">
   <div
     class="hextra-nav-container-blur ..."
   ></div>
   <nav class="hextra-max-navbar-width ...">
     ...
   </nav>
-</div>
+</header>
```

Classes, sticky behavior, and `print:hidden` are all unchanged → zero visual diff. The element stays a direct child of the body shell, so the `<header>`→`banner` mapping holds.

---

## Ready-to-file GitHub ISSUE

**Title:** Navbar is not exposed as a `banner` landmark (`.hextra-nav-container` is a `<div>`)

**Body:**
> ### Describe the bug
> The site navbar is wrapped in a plain `<div class="hextra-nav-container">` (`layouts/_partials/navbar.html`), so rendered pages expose no `banner` landmark. Assistive-technology users navigating by landmark get `navigation`, `main`, `complementary`, and `contentinfo`, but there's no "site header / banner" region to jump to.
>
> ### Expected behavior
> The top-level navbar container should be a `<header>` element (which user agents map to `role="banner"` when it's a top-level region), giving AT users the conventional site-header landmark. This complements the existing skip link and the `<nav>`/`<main>`/`<aside>`/`<footer>` landmarks.
>
> ### Proposed fix
> Change the outer wrapper in `layouts/_partials/navbar.html` from `<div class="hextra-nav-container …">` to `<header class="hextra-nav-container …">` (and the matching closing tag). No class, style, or behavior change — purely semantic. Happy to open a PR.
>
> ### Version
> Hextra v0.12.3.

---

## Ready-to-file PR

**Branch:** `a11y/navbar-banner-landmark`
**Title:** a11y: expose the navbar as a `banner` landmark (`<div>` → `<header>`)

**Body:**
> ### What
> Changes the navbar's outer wrapper in `_partials/navbar.html` from `<div class="hextra-nav-container">` to `<header …>` so the site header is exposed as a `banner` landmark.
>
> ### Why
> The navbar is a body-level region but was a generic `<div>`, so pages had no `banner` landmark — assistive-technology users couldn't jump to the site header by landmark. A `<header>` at this level maps to `role="banner"` per the HTML/ARIA mapping, alongside the existing skip link and `<nav>`/`<main>`/`<aside>`/`<footer>`.
>
> ### Notes
> Only the wrapper element changes — all classes (`hx:sticky hx:top-0 …`, `hx:print:hidden`), the blur layer, and behavior are untouched, so there is no visual change. Verified against v0.12.3.

---

## How to open it (when ready)

```bash
gh repo fork imfing/hextra --clone   # or fork in the UI
cd hextra && git checkout -b a11y/navbar-banner-landmark
# apply the one-element change in layouts/_partials/navbar.html
git commit -am "a11y: expose the navbar as a banner landmark (<div> -> <header>)"
git push -u origin a11y/navbar-banner-landmark
gh pr create --repo imfing/hextra --title "a11y: expose the navbar as a banner landmark (<div> -> <header>)" --body-file <this PR body>
```

## Interim decision for carriekroutil-site

Because the proper fix is upstream, **we are NOT vendoring a local `navbar.html` override** in carriekroutil-site for now. The handbook still meets WCAG 2.1 AA without it. Once the upstream PR is merged and released, a routine Hextra version bump picks it up for free. (If we want the `banner` landmark *before* upstream releases, fall back to the local override described in carriekroutil-site PR #21 / Story 1.5.)
