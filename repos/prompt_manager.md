# prompt_manager

Prompt file management gem used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-10 (seventh run)._ Ran the **dependencies audit** (most overdue): bundle-audit clean, `bundle outdated` still only minitest 6.0.6→6.1.0 + prism 1.9.0→2.1.0 (dev/transitive, lockfile-only = F-8/F-9), suite green 292/0/0 at 100% on Ruby 4.1.0preview1, gemspec floor `>=4.0.9` == README, no Ruby-4 removed-API usage. Improver found three obvious-yes docs-example bugs, all **self-merged into develop as low risk**: [#29](https://github.com/MadBomber/prompt_manager/pull/29) (`git rev-parse --toplevel` → `--show-toplevel` in code-review example, C-1 → develop c2eab17), [#30](https://github.com/MadBomber/prompt_manager/pull/30) (ai-agent example `include` paths `../common/` → `../../common/`, C-2 → b01cc88), [#28](https://github.com/MadBomber/prompt_manager/pull/28) (`PM::VERSION` doc example 1.1.0 → 1.2.0, C-3 → 9219201). Tender rebased #25 and #26 (both green, both still unapproved — left for Dewayne). Responder: #17 already answered, no new comment. No new issues filed (remaining findings need Dewayne's decision or are cosmetic). Prior sixth-run PR #27 (`Metadata#==` docs) merged. develop HEAD 9219201.

_Previously, sixth run:_ adoption audit; #27 (Metadata#== docs, self-merged low risk, 39827bf); #26 opened (CHANGELOG v0.5.1/v0.5.2, F-22, left for Dewayne). Findings F-22 (PR'd), F-23 (reset_directives! README wording, needs decision), F-24 (changelog link-refs, cosmetic), F-25 (= #27, merged).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- _(empty.)_

## Open bomberbot PRs

- [#26](https://github.com/MadBomber/prompt_manager/pull/26) docs: add CHANGELOG entries for v0.5.1 and v0.5.2 (adoption F-22) (**PR open**, targets develop; Test check green; docs-only; unapproved — left for Dewayne, version-stamped CHANGELOG history)
- [#25](https://github.com/MadBomber/prompt_manager/pull/25) test: save and restore ENV in env-touching tests (improver C1 / taste aia#160) (**PR open**, targets develop; Test check green; test-only; unapproved; verified still merges clean)

Merged: [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20), [#21](https://github.com/MadBomber/prompt_manager/pull/21), [#22](https://github.com/MadBomber/prompt_manager/pull/22) (all 2026-11-07, no comments), [#23](https://github.com/MadBomber/prompt_manager/pull/23), [#24](https://github.com/MadBomber/prompt_manager/pull/24) (both 2026-11-09, merge commits, no comments), [#27](https://github.com/MadBomber/prompt_manager/pull/27) (2026-11-10, **self-merged by madbot as low risk** — docs-only `Metadata#==` fix, develop 39827bf), [#29](https://github.com/MadBomber/prompt_manager/pull/29) (2026-11-10, **self-merged low risk** — `--show-toplevel` docs fix, c2eab17), [#30](https://github.com/MadBomber/prompt_manager/pull/30) (2026-11-10, **self-merged low risk** — ai-agent include paths, b01cc88), [#28](https://github.com/MadBomber/prompt_manager/pull/28) (2026-11-10, **self-merged low risk** — PM::VERSION doc example 1.2.0, 9219201).
