# prompt_manager

Prompt file management gem used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-10 (fifth run, resumed)._ #23 and #24 merged (2026-11-09, merge commits, no comments). This visit's security audit (most overdue, first since #19) found the codebase still clean: bundle-audit clean (1353 advisories), S-1's erb-safe escaping held against 5 boundary-bypass attempts, no ReDoS. One new PR, **PR open**: [#25](https://github.com/MadBomber/prompt_manager/pull/25) routes ENV-touching tests through a `with_env` save/restore helper (improver C1 + taste rule from aia#160), test-only, CI green. New security finding S-4 (directive file-arg containment) needs Dewayne's decision, held locally. F-7 re-verified as F-21. Tender/responder queues empty at visit start (verified directly).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- _(empty.)_

## Open bomberbot PRs

- [#25](https://github.com/MadBomber/prompt_manager/pull/25) test: save and restore ENV in env-touching tests (improver C1 / taste aia#160) (**PR open**, targets develop; Test check green; test-only; unapproved)

Merged: [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20), [#21](https://github.com/MadBomber/prompt_manager/pull/21), [#22](https://github.com/MadBomber/prompt_manager/pull/22) (all 2026-11-07, no comments), [#23](https://github.com/MadBomber/prompt_manager/pull/23), [#24](https://github.com/MadBomber/prompt_manager/pull/24) (both 2026-11-09, merge commits, no comments).
