# prompt_manager

Prompt file management gem used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-10 (sixth run)._ Ran the adoption audit (most overdue). Two docs-only PRs: [#27](https://github.com/MadBomber/prompt_manager/pull/27) (corrected the `Metadata#==` docs cell that contradicted the code + its `refute_equal` test — improver C-1) **self-merged into develop as low risk** (39827bf), and **PR open** [#26](https://github.com/MadBomber/prompt_manager/pull/26) (adds the missing CHANGELOG sections for released tags v0.5.1/v0.5.2 — auditor F-22, left for Dewayne as it touches version-stamped history, not `## Unreleased`). #25 still open/unapproved (verified clean). Responder: #17 already answered, no new comment. Triage/dead-code: develop was byte-identical to the fifth-run audit until #27. New findings F-22 (PR'd), F-23 (reset_directives! README wording, needs decision), F-24 (changelog link-refs, cosmetic), F-25 (= #27, merged). No new issues filed.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- _(empty.)_

## Open bomberbot PRs

- [#26](https://github.com/MadBomber/prompt_manager/pull/26) docs: add CHANGELOG entries for v0.5.1 and v0.5.2 (adoption F-22) (**PR open**, targets develop; Test check green; docs-only; unapproved — left for Dewayne, version-stamped CHANGELOG history)
- [#25](https://github.com/MadBomber/prompt_manager/pull/25) test: save and restore ENV in env-touching tests (improver C1 / taste aia#160) (**PR open**, targets develop; Test check green; test-only; unapproved; verified still merges clean)

Merged: [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20), [#21](https://github.com/MadBomber/prompt_manager/pull/21), [#22](https://github.com/MadBomber/prompt_manager/pull/22) (all 2026-11-07, no comments), [#23](https://github.com/MadBomber/prompt_manager/pull/23), [#24](https://github.com/MadBomber/prompt_manager/pull/24) (both 2026-11-09, merge commits, no comments), [#27](https://github.com/MadBomber/prompt_manager/pull/27) (2026-11-10, **self-merged by madbot as low risk** — docs-only `Metadata#==` fix, develop 39827bf).
