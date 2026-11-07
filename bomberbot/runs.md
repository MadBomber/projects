# Runs

Newest first. One entry per run; add each repo as it finishes.

## 2026-11-06 (full run, three lanes)

- Tended: ragdoll #4 and #5 were merged 2026-11-06 with no comments.
- **aia**: new PR [#157](https://github.com/MadBomber/aia/pull/157), fix(test): the unit suite now runs without API keys or a UTF-8 locale (source: dependencies audit). Audit: dependencies (first run; bundle outdated and bundle-audit both clean). Dead-code scan not run; this run's budget went to the audit. Tended PRs: none. Review: `/simplify` plus 2 `/code-review` rounds, converged under the cap. The repo has no PR CI, so there are no checks. Also verified security finding S-1, flagged by the prompt_manager lane (details held locally; needs Dewayne's decision).
- **prompt_manager**: new PR [#19](https://github.com/MadBomber/prompt_manager/pull/19) (source: security; neutral title). Audit: security (first run; bundle-audit clean). Dead-code scan up to 240b105 found nothing removable. Tended PRs: none. aia suite unchanged against the 1.2.0 baseline (1975 tests, 0 failures, 38 errors, all environmental). Review hit the 3-round cap; fixed the trivial items and opened per the ragdoll#5 precedent, overrun explained in the PR. The repo has no PR CI, so there are no checks to go green (finding F-3). Dependabot alerts are disabled.
- **shared_tools**: new PR [#5](https://github.com/MadBomber/shared_tools/pull/5) (source: issue #3). Audit: dependencies. Dead-code scan up to 4ca4469. Tended PRs: none. aia suite matches the 0.7.1 baseline. `/code-review` hit the 3-round cap; opened per the ragdoll#5 rule. Round 2 reverted two round-1 "robustness" additions that caused regressions. The repo has no test CI, so there are no checks.
- **debug_me**: new PR [#8](https://github.com/MadBomber/debug_me/pull/8) (source: adoption audit, the repo's first audit). Dead-code quick scan up to 9e0f9be found nothing. Tended PRs: none. `/code-review` went 3 rounds; fixed the small round-3 items and opened per the ragdoll#5 rule. The repo has no test CI, so no checks ran; the proofs ran locally on Ruby 4.1, 4.0.9 and 2.6.10. Before/after docs screenshots are attached to the PR.

## 2026-11-05 (single repo: ragdoll, one lane, run by hand)

- **ragdoll**: new PRs [#5](https://github.com/MadBomber/ragdoll/pull/5) (source: brain backlog) and [#4](https://github.com/MadBomber/ragdoll/pull/4) (source: CI; the "same check failing on develop" exception). They block each other's CI; together CI mode is green. Backlog cause corrected (not `ruby_llm_config`). No audit ran: brain had a workable item. Dead-code scan not run. Tended PRs: none existed. #5 went past the 3-round review cap; Dewayne chose fix-and-open over give-up. Dependabot alerts are disabled on this repo.
- Not reached: every other repo (this run covered ragdoll only, by request).

## 2026-11-05 (scaffold only, no run)

Brain layout created. No repos visited.
