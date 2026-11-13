# prompt_manager

Prompt file management gem used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-13 (ninth run)._ Dewayne merged all four waiting PRs (#25, #26, #31, #35) on 2026-11-10, so the queue was empty. Ran the second **quality** audit (develop 37420ea): F-38..F-46 plus S-5. Opened three PRs: [#40](https://github.com/MadBomber/prompt_manager/pull/40) (Fixes #32), [#41](https://github.com/MadBomber/prompt_manager/pull/41) (F-34, the eighth run's never-opened PR), [#36](https://github.com/MadBomber/prompt_manager/pull/36) (Fixes #33). Filed issues #37, #38, #39. develop HEAD 37420ea.

_Previously, eighth run (2026-11-10):_ dead-code audit; #31/#35 opened and later merged by Dewayne; issues #32-#34 filed.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Front matter isn't recognised in files with CRLF line endings, and an indented `---` inside a YAML block scalar ends it early (F-38/F-39). One regex fix, verified with the suite green: see the findings file. Ship it as one lib PR and run aia's suite. Added 2026-11-13; the visit's three PR slots were full.

## Open bomberbot PRs

- [#41](https://github.com/MadBomber/prompt_manager/pull/41) fix: let directives named like Kernel methods be called from templates (F-34) (**PR open**, targets develop; Test green; lib change; aia suite 2001/0/0 before and after; review hit the 3-round cap, with deferred items listed in the body; waits for Dewayne)
- [#40](https://github.com/MadBomber/prompt_manager/pull/40) fix: accept nil and Pathname prompts_dir (Fixes #32) (**PR open**, targets develop; Test green; lib change; aia suite 2001/0/0 before and after; review hit the 3-round cap with no defects; waits for Dewayne)
- [#36](https://github.com/MadBomber/prompt_manager/pull/36) docs: strict mode raises for unknown calls with args (Fixes #33) (**PR open**, targets develop; Test green; docs + test, review clean in 2 rounds; not self-merged because `docs/` deploys Pages on push to develop; waits for Dewayne)

#40 and #41 each add a CHANGELOG `## Unreleased` line, so whichever merges second will need a rebase.

Merged: [#25](https://github.com/MadBomber/prompt_manager/pull/25), [#26](https://github.com/MadBomber/prompt_manager/pull/26), [#31](https://github.com/MadBomber/prompt_manager/pull/31), [#35](https://github.com/MadBomber/prompt_manager/pull/35) (all 2026-11-10 by Dewayne, no comments), [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20), [#21](https://github.com/MadBomber/prompt_manager/pull/21), [#22](https://github.com/MadBomber/prompt_manager/pull/22) (all 2026-11-07, no comments), [#23](https://github.com/MadBomber/prompt_manager/pull/23), [#24](https://github.com/MadBomber/prompt_manager/pull/24) (both 2026-11-09, merge commits, no comments), [#27](https://github.com/MadBomber/prompt_manager/pull/27) (2026-11-10, **self-merged by madbot as low risk** — docs-only `Metadata#==` fix, develop 39827bf), [#29](https://github.com/MadBomber/prompt_manager/pull/29) (2026-11-10, **self-merged low risk** — `--show-toplevel` docs fix, c2eab17), [#30](https://github.com/MadBomber/prompt_manager/pull/30) (2026-11-10, **self-merged low risk** — ai-agent include paths, b01cc88), [#28](https://github.com/MadBomber/prompt_manager/pull/28) (2026-11-10, **self-merged low risk** — PM::VERSION doc example 1.2.0, 9219201).
