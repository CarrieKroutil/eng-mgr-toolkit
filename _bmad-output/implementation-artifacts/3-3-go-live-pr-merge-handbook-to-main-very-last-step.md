# Story 3.3: Go-live PR — merge handbook to main (very last step)

Status: done

## Story

As Carrie,
I want a PR to merge `handbook` into `main`,
so that the handbook deploys live.

## Acceptance Criteria

1. **Given** UAT sign-off, **When** I open a PR from `handbook` → `main` in `carriekroutil-site`, **Then** it bundles the complete handbook work. ✅
2. **Given** the PR is merged to `main`, **Then** the single inherited Amplify pipeline builds and deploys to `carriekroutil.com/handbook`, **And** no second pipeline, repo, or domain is introduced. ✅
3. **Given** go-live, **Then** this is the only `main` merge, and nothing was deployed before it. ✅

## Outcome

- **PR #21** (`carriekroutil-site`, `handbook` → `main`) — the single long-lived go-live PR (Epics 1–3). Title/body updated for go-live before merge.
- **Merged 2026-06-25T23:52:58Z**, merge commit **`2a95903`** ("Merge pull request #21 from CarrieKroutil/handbook"). Merge commit (not squash) — preserves Epic 1–3 history. **Carrie performed the merge** (her call on the irreversible production deploy).
- Pre-merge state verified: `MERGEABLE` / `CLEAN`, `handbook` 8 ahead / 0 behind `main`, 72 files / +3,706. Clean `hugo --gc --minify`.
- Amplify (the single inherited pipeline) built and deployed to **carriekroutil.com/handbook** — verified HTTP 200 serving the Start Here landing. No new pipeline/repo/domain.
- This was the only `main` merge; nothing deployed before it (Amplify branch previews disabled by design).

## Change Log

| Date | Change |
|------|--------|
| 2026-06-25 | Updated PR #21 title → "Engineering Manager Handbook (Epics 1–3) — go-live" and rewrote the body as a go-live summary. Carrie merged (`2a95903`); Amplify deployed; prod `/handbook/` live (HTTP 200). Status → done. |
