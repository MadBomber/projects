# prompt_manager

Prompt file management gem used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-09 (fourth run)._ #22 merged (2026-11-07, merge commit, no comments). This visit's first-ever quality audit found a clean codebase (flay 0, flog max 34; new findings F-18/F-19/F-20, all decisions or trivial). Two new PRs, both independent, both CI green, **PR open**: [#24](https://github.com/MadBomber/prompt_manager/pull/24) adds `docs/llms.txt` (F-12), [#23](https://github.com/MadBomber/prompt_manager/pull/23) stops the coverage gate arming on `CI=false` (F-10). Responder commented on #17 (obsolete: the 0.x storage adapter it blames was removed in the 1.0 rewrite). Review queue was empty at visit start.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- _(empty — F-11 shipped as #22, F-12 as #24, F-10 as #23.)_

## Open bomberbot PRs

- [#24](https://github.com/MadBomber/prompt_manager/pull/24) docs: add llms.txt for AI assistant discoverability (F-12) (**PR open**, targets develop; Test check green; unapproved)
- [#23](https://github.com/MadBomber/prompt_manager/pull/23) test: only arm the coverage gate when CI is literally true (F-10) (**PR open**, targets develop; Test check green; unapproved)

Merged: [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20), [#21](https://github.com/MadBomber/prompt_manager/pull/21), [#22](https://github.com/MadBomber/prompt_manager/pull/22) (all 2026-11-07, no comments).
