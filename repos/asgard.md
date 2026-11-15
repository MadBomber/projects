# asgard

Thor-based task runner driven by .loki files; the other monitored repos, dev, and the `*_project` workspaces use it. Prefer `asgard <task>` where a repo has a `.loki`, otherwise `bundle exec rake test`. asgard itself has no Rakefile: its suite is `ruby -Ilib:test test/parallel_runner.rb`. A change to a public method must not break dev; run dev's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-15 (fourth visit)._ Dewayne merged #20-#24, fixed #25-#29 and #19 himself (#30-#34) and released 0.6.1; this visit merged #36 as low risk (develop 4ee31fe), opened #37 and #39 (stacked on #37), filed #38.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-44: a leading `--debug`/`--verbose` before `jobs list` / `schedule list` / `watcher list` aborts outside a project. **PR open** ([#37](https://github.com/MadBomber/asgard/pull/37))
- F-45: `asgard --nope` reports `Could not find command "__nope"`. **PR open** ([#39](https://github.com/MadBomber/asgard/pull/39), stacked on #37)
- F-46: `asgard --debug=true build` prints usage and exits 0 without running. Issue [#38](https://github.com/MadBomber/asgard/issues/38); Dewayne usually fixes filed issues himself, check first.
- F-47: develop lacks main's released 0.6.1 CHANGELOG text (`61a1111`). Needs Dewayne (dewayne.md).

## Open bomberbot PRs

- [#37](https://github.com/MadBomber/asgard/pull/37) fix(cli): keep list commands machine-wide behind --debug or --verbose (**PR open**, 2026-11-15; F-44, Refs #27)
- [#39](https://github.com/MadBomber/asgard/pull/39) fix(cli): name an unknown leading option as an option (**PR open**, 2026-11-15; F-45; stacks on #37)

## Merged bomberbot PRs

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
