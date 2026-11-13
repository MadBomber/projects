# asgard

Thor-based task runner driven by .loki files; the other monitored repos, dev, and the `*_project` workspaces use it. Prefer `asgard <task>` where a repo has a `.loki`, otherwise `bundle exec rake test`. asgard itself has no Rakefile: its suite is `ruby -Ilib:test test/parallel_runner.rb`. A change to a public method must not break dev; run dev's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-13 (second visit)._ develop unchanged at 4f3add1, CI green; #20/#21 still await review. This visit: adoption audit (F-31..F-36), three new PRs (#22 load hint, #23 fixes #18, #24 docs accuracy), issues #25 and #26 filed.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-24: the dependency-arity error should name the declaring task, like #14's fix. **PR open** ([#21](https://github.com/MadBomber/asgard/pull/21))
- `truncate_log` (4f3add1) missing from README, CHANGELOG and `docs/files.md`. **PR open** ([#20](https://github.com/MadBomber/asgard/pull/20))
- Issue #18 items 1-2 (truncate_log no-log-yet message, watcher help). **PR open** ([#23](https://github.com/MadBomber/asgard/pull/23)); item 3 and `project/NAME` wait on Dewayne (inbox).
- F-32: reserved helper name reported at task_dsl.rb, not the .loki line. Issue [#25](https://github.com/MadBomber/asgard/issues/25); small lib fix, good next-visit PR.
- F-36: schedule_root/watch_root duplicate project_root. Issue [#26](https://github.com/MadBomber/asgard/issues/26); do after #23 merges (same files).

## Open bomberbot PRs

- [#22](https://github.com/MadBomber/asgard/pull/22) feat(load): hint when a DSL word is outside class Tasks (**PR open**, 2026-11-13; F-35)
- [#23](https://github.com/MadBomber/asgard/pull/23) fix(logs): truncate_log NAME skips an entry with no log yet (**PR open**, 2026-11-13; Fixes #18)
- [#24](https://github.com/MadBomber/asgard/pull/24) docs: fix reserved helper name, doctor exit, robots example (**PR open**, 2026-11-13; F-31/F-33/F-34)
- [#20](https://github.com/MadBomber/asgard/pull/20) docs: list truncate_log in README, CHANGELOG and files.md (**PR open**, 2026-11-13)
- [#21](https://github.com/MadBomber/asgard/pull/21) fix(deps): name declaring tasks in dependency arity errors (**PR open**, 2026-11-13; F-24, Refs #14)

## Merged bomberbot PRs

- [#16](https://github.com/MadBomber/asgard/pull/16) fix(jobs): restart a job whose trampoline is still exiting (merged 2026-11-10)
- [#12](https://github.com/MadBomber/asgard/pull/12) fix(doctor): report exit or abort during .loki load (merged 2026-11-10)
- [#11](https://github.com/MadBomber/asgard/pull/11) docs: add llms.txt index of the docs site (merged 2026-11-10)
- [#9](https://github.com/MadBomber/asgard/pull/9) chore(gemspec): link the docs site and issue tracker (merged 2026-11-07)
- [#8](https://github.com/MadBomber/asgard/pull/8) refactor(watch): drop the Ractor#take fallback in join_worker (merged 2026-11-07)
- [#7](https://github.com/MadBomber/asgard/pull/7) fix(test): pass the suite when no locale is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/asgard/pull/6) Create schedule log directories owner-only (0700) (merged 2026-11-07)
