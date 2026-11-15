# asgard

Thor-based task runner driven by .loki files; the other monitored repos, dev, and the `*_project` workspaces use it. Prefer `asgard <task>` where a repo has a `.loki`, otherwise `bundle exec rake test`. asgard itself has no Rakefile: its suite is `ruby -Ilib:test test/parallel_runner.rb`. A change to a public method must not break dev; run dev's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-15 (sixth visit)._ Queue full (4 PRs, no approvals); tender rebased #37/#39/#40/#41 onto d87e1e2, all green. Dependencies audit ran; filed #44, #45, #46. No new PRs.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-44: a leading `--debug`/`--verbose` before `jobs list` / `schedule list` / `watcher list` aborts outside a project. **PR open** ([#37](https://github.com/MadBomber/asgard/pull/37))
- F-45: `asgard --nope` reports `Could not find command "__nope"`. **PR open** ([#39](https://github.com/MadBomber/asgard/pull/39), stacked on #37)
- F-46: `asgard --debug=true build` prints usage and exits 0 without running. Issue [#38](https://github.com/MadBomber/asgard/issues/38). **PR open** ([#40](https://github.com/MadBomber/asgard/pull/40), stacked on #39; also hidden and dashed task names after a leading flag)
- F-49: `current_task` missing from docs/reference.md's task-body table. **PR open** ([#41](https://github.com/MadBomber/asgard/pull/41))
- F-50: `asgard TASK --help` runs the task. Issue [#43](https://github.com/MadBomber/asgard/issues/43); Dewayne usually fixes filed issues himself, check first.
- F-60: README `[!INFO]` callout renders as literal text. Issue [#45](https://github.com/MadBomber/asgard/issues/45); one line.
- F-56: Ractor experimental warning opens every watcher.log. Issue [#44](https://github.com/MadBomber/asgard/issues/44).
- F-61: `schedule every:` error suggests `3.minutes` and hides the value. Issue [#46](https://github.com/MadBomber/asgard/issues/46).
- F-62: `tree` header/footer untested (test-only, low risk).
- F-53: `dep_results` doc comment names a `target` argument it doesn't take (comment only).
- F-47: develop lacks main's released 0.6.1 CHANGELOG text (`61a1111`). Needs Dewayne (dewayne.md).

## Open bomberbot PRs

- [#37](https://github.com/MadBomber/asgard/pull/37) fix(cli): keep list commands machine-wide behind --debug or --verbose (**PR open**, 2026-11-15; F-44, Refs #27)
- [#39](https://github.com/MadBomber/asgard/pull/39) fix(cli): name an unknown leading option as an option (**PR open**, 2026-11-15; F-45; stacks on #37)
- [#40](https://github.com/MadBomber/asgard/pull/40) fix(cli): every form of a leading --debug or --verbose runs the task named after it (**PR open**, 2026-11-15; F-46, Fixes #38; stacks on #39)
- [#41](https://github.com/MadBomber/asgard/pull/41) docs: list current_task in the task-body reference (**PR open**, 2026-11-15; F-49; docs/ deploys, not self-merged)

## Merged bomberbot PRs

- [#42](https://github.com/MadBomber/asgard/pull/42) test: cover dep_result and dep_results (merged as low risk by madbot 2026-11-15, d87e1e2; F-48)
- [#36](https://github.com/MadBomber/asgard/pull/36) docs: say only tests call Changes.forget (merged as low risk by madbot 2026-11-15, 4ee31fe; F-18)
- [#24](https://github.com/MadBomber/asgard/pull/24) docs: fix reserved helper name, doctor exit, robots example (merged 2026-11-13)
- [#23](https://github.com/MadBomber/asgard/pull/23) fix(logs): truncate_log NAME skips an entry with no log yet (merged 2026-11-13)
- [#22](https://github.com/MadBomber/asgard/pull/22) feat(load): hint when a DSL word is outside class Tasks (merged 2026-11-13)
- [#21](https://github.com/MadBomber/asgard/pull/21) fix(deps): name declaring tasks in dependency arity errors (merged 2026-11-13)
- [#20](https://github.com/MadBomber/asgard/pull/20) docs: list truncate_log in README, CHANGELOG and files.md (merged 2026-11-13)
- [#16](https://github.com/MadBomber/asgard/pull/16) fix(jobs): restart a job whose trampoline is still exiting (merged 2026-11-10)
- [#12](https://github.com/MadBomber/asgard/pull/12) fix(doctor): report exit or abort during .loki load (merged 2026-11-10)
- [#11](https://github.com/MadBomber/asgard/pull/11) docs: add llms.txt index of the docs site (merged 2026-11-10)
- [#9](https://github.com/MadBomber/asgard/pull/9) chore(gemspec): link the docs site and issue tracker (merged 2026-11-07)
- [#8](https://github.com/MadBomber/asgard/pull/8) refactor(watch): drop the Ractor#take fallback in join_worker (merged 2026-11-07)
- [#7](https://github.com/MadBomber/asgard/pull/7) fix(test): pass the suite when no locale is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/asgard/pull/6) Create schedule log directories owner-only (0700) (merged 2026-11-07)
