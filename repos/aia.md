# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-10 (09:11 visit)._ **Skipped new work: review queue full.** Tender found all three bomberbot PRs still open and unapproved (reviewDecision empty), rebased each onto develop, and fixed CHANGELOG `[Unreleased]` collisions on [#176](https://github.com/MadBomber/aia/pull/176) and [#172](https://github.com/MadBomber/aia/pull/172) (sibling `### Fixed` bullets had concatenated); all three MERGEABLE, suite green, awaiting Dewayne. develop unchanged at 9a00307. Nothing approved → nothing merged. Ran the **adoption audit** (most overdue, re-run after the README fixes landed): mkdocs `--strict` clean, docs/reference accurate, recent README fixes confirmed correct; new findings F-41 (README `/paste` omits `/clipboard` alias → issue [#180](https://github.com/MadBomber/aia/issues/180)) and F-42 (full README-vs-registered-directive gap, needs Dewayne → inbox). Filed three issues from verified carried findings: [#180](https://github.com/MadBomber/aia/issues/180) (F-41), [#181](https://github.com/MadBomber/aia/issues/181) (F-29 duplicate `model_opts`), [#182](https://github.com/MadBomber/aia/issues/182) (F-30 duplicate `extract_model_id`). No issue comments warranted (responder: every open issue is bomberbot backlog with no third-party comment, or already ends in a signed BomberBot analysis with the code unchanged). Self-merges #166/#171/#173/#178 still intact on develop, unreverted.

_Prior (02:45 visit)._ Opened three issue/finding PRs and self-merged the docs one. [#178](https://github.com/MadBomber/aia/pull/178) (README drops unregistered `/shell`/`/next`/`/pipeline` rows, Fixes #175) **merged low risk by madbot**; develop to 9a00307. Lib PRs #176/#177 left open for Dewayne. Ran the dependencies audit (develop 4cdf934): clean; new F-39 (`word_wrapper` not Ruby-4-clean, needs Dewayne), F-40 (const_set VERSION warnings → issue #179).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Open issues a PR can resolve: [#172](https://github.com/MadBomber/aia/pull/172) covers #169, [#176](https://github.com/MadBomber/aia/pull/176) covers #174, [#177](https://github.com/MadBomber/aia/pull/177) covers F-27 (all rebased, MERGEABLE, awaiting Dewayne). Issue-resolving obvious-yes targets once the queue clears: [#179](https://github.com/MadBomber/aia/issues/179) (const_set VERSION warnings, F-40 — `$VERBOSE` save/restore), [#180](https://github.com/MadBomber/aia/issues/180) (README `/paste` → add `/clipboard` alias cell, F-41, docs-only), [#181](https://github.com/MadBomber/aia/issues/181) (delete duplicate `model_opts`, delegate to `Crew.model_opts`, F-29), [#182](https://github.com/MadBomber/aia/issues/182) (share `extract_model_id`, F-30).
- Quality DRY refactors from the 2026-11-10 audit (each a small, provable, zero-behavior-change PR): **F-27 shipped in #177.** **F-29 → issue #181, F-30 → issue #182.** Remaining un-filed: F-28 (config section writers), F-31 (output-append idiom), F-32 (duration formatter — taste caveat), F-33 (tool-path require loop), F-34–F-36. See findings file. Stagger them — sibling CHANGELOG `[Unreleased]` lines conflict on rebase.
- F-5 redundant per-file `require 'ostruct'`/`require_relative` in test files is a low-value removal (pairs with F-40).

## Open bomberbot PRs

- [#176](https://github.com/MadBomber/aia/pull/176) fix: case-insensitive `state_setting?` directive matching (resolves #174/F-37) (**PR open** — awaiting Dewayne; lib change + test, not low-risk; rebased onto develop 2026-11-10 + CHANGELOG collision fixed, suite 2000/0)
- [#177](https://github.com/MadBomber/aia/pull/177) refactor: share `split_skills` via `SkillUtils` (F-27) (**PR open** — awaiting Dewayne; lib change, not low-risk; rebased onto develop 2026-11-10, suite 2001/0)
- [#172](https://github.com/MadBomber/aia/pull/172) fix(docs): drop dead asset and CDN refs in mkdocs (resolves #169/F-21-F-22) (**PR open** — awaiting Dewayne; docs-deploy config, not low-risk; rebased onto develop 2026-11-10 + CHANGELOG collision fixed)

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
