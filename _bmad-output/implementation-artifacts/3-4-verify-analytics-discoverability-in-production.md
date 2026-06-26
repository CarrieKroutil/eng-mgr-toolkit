# Story 3.4: Verify analytics & discoverability in production

Status: done

## Story

As Carrie,
I want to confirm analytics and crawlability post-launch,
so that I can measure traffic and search engines index the handbook.

## Acceptance Criteria

1. **Given** the deployed handbook, **When** pages are viewed in production, **Then** GoatCounter records `/handbook` page views (cookieless, no PII, production only). ✅ *(instrumentation verified; see note)*
2. **Given** discoverability, **Then** handbook pages appear in the sitemap and are crawlable (indexable robots policy). ✅
3. **Given** privacy, **Then** no consent banner is introduced. ✅

## Verification (prod, carriekroutil.com — 2026-06-25)

| Check | Result |
|-------|--------|
| `/handbook/` live | HTTP 200, serving the Start Here landing (`ck-start-here`, H1 present) |
| Sitemap coverage | **66** handbook URLs in `sitemap.xml` (root + 12 sections + 53 topics) |
| Crawlable | `robots.txt`: `User-agent: * / Allow: /` + `Sitemap:` reference |
| Indexable | no `noindex` / robots meta on handbook pages |
| GoatCounter | snippet present (`carrie.goatcounter.com/count` via `gc.zgo.at/count.js`), cookieless, no PII |
| Production-only | GoatCounter snippet **absent** on the local `hugo server` (0 matches) → prod-only |
| Consent banner | none |

**Note / hand-off:** the GoatCounter *instrumentation* is correctly installed, cookieless, and prod-only. **Carrie confirmed (2026-06-25) the GoatCounter dashboard is recording `/handbook` views** — AC #1 fully closed.

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Post-go-live prod verification: sitemap (66 handbook URLs), robots `Allow: /` + sitemap ref, no noindex, GoatCounter present + cookieless + prod-only, no consent banner. All ACs met. Status → done. Epic 3 complete; handbook live. |
