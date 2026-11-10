# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-10 (02:45 visit)._ Opened three issue/finding PRs and self-merged the docs one. [#178](https://github.com/MadBomber/aia/pull/178) (README drops unregistered `/shell`/`/next`/`/pipeline` directive rows+examples, Fixes #175) **merged low risk by madbot** (docs-only, ~46 lines, clean review); develop now at 9a00307. Two lib PRs are open for Dewayne (not low-risk): [#176](https://github.com/MadBomber/aia/pull/176) (`state_setting?` now case-insensitive via `.downcase`, Fixes #174 — one-line fix + test, suite 2000/0) and [#177](https://github.com/MadBomber/aia/pull/177) (DRY: move duplicated `split_skills` into `SkillUtils`, F-27 — zero behavior change). [#172](https://github.com/MadBomber/aia/pull/172) (dead mkdocs assets, Fixes #169) still open for Dewayne. Ran the **dependencies audit** (develop 4cdf934, most overdue): bundle-audit clean, outdated gems all transitive, no Ruby-4 removed APIs in lib/bin, hermeticity green (1998/0 + all 127 files standalone). New findings F-39 (`word_wrapper` not Ruby-4-clean, needs Dewayne) and F-40 (test `const_set` VERSION warnings → filed as issue [#179](https://github.com/MadBomber/aia/issues/179)). No issue comments warranted (develop unmoved since last visit; threads already carry BomberBot analysis). Self-merges #166/#171/#173 still intact on develop, unreverted.

_Prior (00:59 visit)._ Queue cleared and four PRs shipped. Dewayne merged #167 (skills security) and #168 (SimpleCov); rebased #166 onto develop + merged low risk, built+merged [#171](https://github.com/MadBomber/aia/pull/171) (Fixes #170) and [#173](https://github.com/MadBomber/aia/pull/173) (Fixes #165) as low risk; ran the first quality audit (F-27..F-36); filed #174 and #175.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Open issues a PR can resolve: [#172](https://github.com/MadBomber/aia/pull/172) covers #169, [#176](https://github.com/MadBomber/aia/pull/176) covers #174, [#177](https://github.com/MadBomber/aia/pull/177) covers F-27 (all awaiting Dewayne). [#179](https://github.com/MadBomber/aia/issues/179) (test `const_set` VERSION warnings, F-40) is the next obvious-yes issue-resolving target — wrap the four `const_set` calls in a `$VERBOSE` save/restore (pairs with F-5).
- Quality DRY refactors from the 2026-11-10 audit (each a small, provable, zero-behavior-change PR): **F-27 shipped in #177.** Remaining: F-28 (config section writers), F-29 (`model_opts`), F-30 (`extract_model_id`), F-31 (output-append idiom), F-32 (duration formatter — taste caveat), F-33 (tool-path require loop), F-34–F-36. See findings file. Stagger them — sibling CHANGELOG `[Unreleased]` lines conflict on rebase.
- F-5 redundant per-file `require 'ostruct'`/`require_relative` in test files is a low-value removal (pairs with F-40).

## Open bomberbot PRs

- [#176](https://github.com/MadBomber/aia/pull/176) fix: case-insensitive `state_setting?` directive matching (resolves #174/F-37) (**PR open** — awaiting Dewayne; lib change + test, not low-risk; review clean round 1, suite 2000/0)
- [#177](https://github.com/MadBomber/aia/pull/177) refactor: share `split_skills` via `SkillUtils` (F-27) (**PR open** — awaiting Dewayne; lib change, not low-risk; zero behavior change, review clean round 1, suite 2001/0)
- [#172](https://github.com/MadBomber/aia/pull/172) fix(docs): drop dead asset and CDN refs in mkdocs (resolves #169/F-21-F-22) (**PR open** — awaiting Dewayne; docs-deploy config, not low-risk)

## Merged bomberbot PRs

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
