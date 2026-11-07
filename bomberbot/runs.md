# Runs

Newest first. One entry per run; add each repo as it finishes.

## 2026-11-06 (full run, three lanes)

- Tended: ragdoll #4 and #5 were merged 2026-11-06 with no comments.
- **prompt_manager**: new PR [#19](https://github.com/MadBomber/prompt_manager/pull/19) (source: security; neutral title). Audit: security (first run; bundle-audit clean). Dead-code scan up to 240b105 found nothing removable. Tended PRs: none. aia suite unchanged against the 1.2.0 baseline (1975 tests, 0 failures, 38 errors, all environmental). Review hit the 3-round cap; fixed the trivial items and opened per the ragdoll#5 precedent, overrun explained in the PR. The repo has no PR CI, so there are no checks to go green (finding F-3). Dependabot alerts are disabled.

## 2026-11-05 (single repo: ragdoll, one lane, run by hand)

- **ragdoll**: new PRs [#5](https://github.com/MadBomber/ragdoll/pull/5) (source: brain backlog) and [#4](https://github.com/MadBomber/ragdoll/pull/4) (source: CI; the "same check failing on develop" exception). They block each other's CI; together CI mode is green. Backlog cause corrected (not `ruby_llm_config`). No audit ran: brain had a workable item. Dead-code scan not run. Tended PRs: none existed. #5 went past the 3-round review cap; Dewayne chose fix-and-open over give-up. Dependabot alerts are disabled on this repo.
- Not reached: every other repo (this run covered ragdoll only, by request).

## 2026-11-05 (scaffold only, no run)

Brain layout created. No repos visited.
