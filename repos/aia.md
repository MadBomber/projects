# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-10 (00:59 visit)._ Queue cleared and four PRs shipped. Dewayne merged #167 (skills security) and #168 (SimpleCov) since the last visit; I rebased #166 (README config tables, resolves #163) onto develop and merged it as low risk, then built and merged two issue-resolving PRs as low risk — [#171](https://github.com/MadBomber/aia/pull/171) (README `/ruby` gate note, Fixes #170) and [#173](https://github.com/MadBomber/aia/pull/173) (test harness now fails the run on an unknown CLI option, Fixes #165). [#172](https://github.com/MadBomber/aia/pull/172) (remove dead mkdocs assets + polyfill.io, Fixes #169) is open for Dewayne — `mkdocs.yml` is docs-deploy config, not low risk. Ran the first **quality audit** (develop 90082b0): flog/flay/reek/rubocop within house thresholds; the only signal is cross-file duplication (F-27..F-36). Answered #163 on-issue. Filed [#174](https://github.com/MadBomber/aia/issues/174) (`state_setting?` is case-sensitive while directives are case-insensitive — a real chat bug) and [#175](https://github.com/MadBomber/aia/issues/175) (README documents removed/never-existent `/shell`, `/next`, `/pipeline` directives). develop now at 4cdf934.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Open issues a PR can resolve: [#172](https://github.com/MadBomber/aia/pull/172) already covers #169 (awaiting Dewayne). [#174](https://github.com/MadBomber/aia/issues/174) (one-line `.downcase` fix + test) and [#175](https://github.com/MadBomber/aia/issues/175) (README drop false directive rows/examples) are the next obvious-yes targets.
- Quality DRY refactors from the 2026-11-10 audit (each a small, provable, zero-behavior-change PR): **F-27** (`split_skills` duplicated across recruit/reskill tools → move to `SkillUtils`; strongest), F-28..F-36. See findings file.
- F-5 redundant per-file `require 'ostruct'`/`require_relative` in test files is a low-value removal.

## Open bomberbot PRs

- [#172](https://github.com/MadBomber/aia/pull/172) fix(docs): drop dead asset and CDN refs in mkdocs (resolves #169/F-21-F-22) (**PR open** — awaiting Dewayne; docs-deploy config, not low-risk)

## Merged bomberbot PRs

- [#173](https://github.com/MadBomber/aia/pull/173) test: fail the run on an unknown CLI option (merged low risk by madbot 2026-11-10, resolves #165/F-14)
- [#171](https://github.com/MadBomber/aia/pull/171) docs: note /ruby directive needs --allow-ruby-eval (merged low risk by madbot 2026-11-10, resolves #170/F-23)
- [#168](https://github.com/MadBomber/aia/pull/168) test: replace deprecated SimpleCov calls (merged by Dewayne 2026-11-09, resolves #164/F-17)
- [#167](https://github.com/MadBomber/aia/pull/167) fix(skills): recruit and reskill tools accept only skill-directory ids (merged by Dewayne 2026-11-09, security S-2)
- [#166](https://github.com/MadBomber/aia/pull/166) docs: correct README configuration tables to match defaults.yml (merged low risk by madbot 2026-11-10, resolves #163/F-18)
- [#160](https://github.com/MadBomber/aia/pull/160) fix: DelegateHandler and Foreman tool require their classes (merged 2026-11-07)
- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (merged 2026-11-07)
- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (merged 2026-11-07)
- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
