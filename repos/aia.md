# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-12 (00:59 visit)._ Dewayne rebase-merged #172/#176/#177 before the visit, so the queue was empty. Opened three PRs from the Backlog issues: [#184](https://github.com/MadBomber/aia/pull/184) (README aliases, Fixes #180) and [#185](https://github.com/MadBomber/aia/pull/185) (VERSION warnings, Fixes #179) were **merged low risk by madbot**, so develop is now 2c1d77b. [#183](https://github.com/MadBomber/aia/pull/183) (dedupe `model_opts`, Fixes #181) is open for Dewayne. Ran the dead-code audit (F-43 → issue #187); the improver found that the README documents removed log flags (F-44 → issue #186). The responder posted "fixed on develop" notes on #164/#165/#169/#170/#174/#175. Suite 2003/0. The repo has no test CI, so PRs show zero checks.

_Prior (2026-11-10 09:11 visit)._ Skipped new work because the review queue was full. Tended #172/#176/#177 and filed #180-#182.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Open issues a PR can resolve: [#182](https://github.com/MadBomber/aia/issues/182) (share `extract_model_id`, F-30; touches mention_router/chat_loop/session_tracker), [#186](https://github.com/MadBomber/aia/issues/186) (README removed `--info/--warn/--error/--fatal` → `--log-level`, F-44, docs-only, low-risk candidate), [#187](https://github.com/MadBomber/aia/issues/187) (remove write-only `TurnState#active_mcp_servers`, F-43, removal). #181 → **PR open** [#183](https://github.com/MadBomber/aia/pull/183).
- Quality DRY refactors still un-filed: F-28 (config `reset_section`/`merge_into_section` identical), F-31 (output-append idiom), F-33 (tool-path require loop, `tool_loader.rb:62-72` vs `validator.rb:383-395`), F-36 (network_builder setup), F-32 has a taste caveat. Re-verified on c04ce8e by triage. Do them one at a time, because sibling CHANGELOG `[Unreleased]` lines conflict on rebase.
- `PromptHandler::SHORTHAND_KEYS` (part of F-13) is verified test-only. Removing it is a public-constant change, so it goes in the F-13 batch decision (inbox).
- F-5: redundant per-file `require 'ostruct'`/`require_relative` lines in test files. Low value.

## Open bomberbot PRs

- [#183](https://github.com/MadBomber/aia/pull/183) refactor(spawn): reuse `Crew.model_opts` in SpawnHandler (Fixes #181/F-29). **PR open**, awaiting Dewayne. It is a lib change, so not low risk. Review clean in 1 round, suite 2003/0.

## Merged bomberbot PRs

- [#185](https://github.com/MadBomber/aia/pull/185) test: swap VERSION constants without redefinition warnings (merged low risk by madbot 2026-11-12 → 2c1d77b, resolves #179/F-40)
- [#184](https://github.com/MadBomber/aia/pull/184) docs: README aliases for /paste and /available_models (merged low risk by madbot 2026-11-12 → 1b1f227, resolves #180/F-41)
- [#176](https://github.com/MadBomber/aia/pull/176) fix: case-insensitive `state_setting?` (rebase-merged by Dewayne 2026-11-10, resolves #174)
- [#177](https://github.com/MadBomber/aia/pull/177) refactor: share `split_skills` via SkillUtils (rebase-merged by Dewayne 2026-11-10, F-27)
- [#172](https://github.com/MadBomber/aia/pull/172) fix(docs): drop dead mkdocs asset/CDN refs (rebase-merged by Dewayne 2026-11-10, resolves #169)
- [#178](https://github.com/MadBomber/aia/pull/178) docs: fix README's unregistered /shell /next /pipeline directives (merged low risk by madbot 2026-11-10, resolves #175/F-38)
- [#173](https://github.com/MadBomber/aia/pull/173) test: fail the run on an unknown CLI option (merged low risk by madbot 2026-11-10, resolves #165/F-14)
- [#171](https://github.com/MadBomber/aia/pull/171) docs: note /ruby directive needs --allow-ruby-eval (merged low risk by madbot 2026-11-10, resolves #170/F-23)
- [#168](https://github.com/MadBomber/aia/pull/168) test: replace deprecated SimpleCov calls (merged by Dewayne 2026-11-09, resolves #164/F-17)
- [#167](https://github.com/MadBomber/aia/pull/167) fix(skills): recruit and reskill tools accept only skill-directory ids (merged by Dewayne 2026-11-09, security S-2)
- [#166](https://github.com/MadBomber/aia/pull/166) docs: correct README configuration tables to match defaults.yml (merged low risk by madbot 2026-11-10, resolves #163/F-18)
- [#160](https://github.com/MadBomber/aia/pull/160) fix: DelegateHandler and Foreman tool require their classes (merged 2026-11-07)
- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (merged 2026-11-07)
- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (merged 2026-11-07)
- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
