# dev

Shared Asgard task libraries (.loki) for madbomber repos. Not a gem: its suite is whatever `test/` holds plus a clean load of every `.loki` file.

## Links

- GitHub: https://github.com/MadBomber/dev
- RubyGems: https://rubygems.org/gems/dev
- Issues: https://github.com/MadBomber/dev/issues

## Status

_Updated 2026-11-13 (eighth run)._ Queue still full (#22, #25, #26 open, none approved), so no new PRs. The dependencies audit ran and found F-53, filed as #29. `develop` is still 657e6bd.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-5: drop the header comments copied from the vbr project (after F-6 is decided).
- F-37: keep git `fatal:` noise out of `branch_report` stderr. [dev#22](https://github.com/MadBomber/dev/pull/22) **PR open**
- F-13: escape `|` in branch_report author cells (and `|` in branch names). [dev#26](https://github.com/MadBomber/dev/pull/26) **PR open**, stacked on #22.
- F-39: stale header comments and labels in quality.loki (plus F-46 ArchSpec message, F-49 reek/flay load text); after Dewayne's #19 lands and his `.quality/` decision.
- F-45: friendly abort on an invalid `STATUS_DATE` / `STATUS_WEEK_OF`. [dev#25](https://github.com/MadBomber/dev/pull/25) **PR open** (strict ISO parsing left for Dewayne).

## Open bomberbot PRs

- [dev#22](https://github.com/MadBomber/dev/pull/22): fix(branch_report): keep git's stderr off the terminal (F-37). **PR open**
- [dev#25](https://github.com/MadBomber/dev/pull/25): fix(status_report): one-line abort on an unreadable date env var (F-45). **PR open**
- [dev#26](https://github.com/MadBomber/dev/pull/26): fix(branch_report): keep "|" in names from breaking rows (F-13). Stacks on #22; retarget to `develop` after #22 merges. **PR open**

## Open bomberbot issues

- [dev#7](https://github.com/MadBomber/dev/issues/7) gem_version can read the wrong version.rb (F-31)
- [dev#8](https://github.com/MadBomber/dev/issues/8) asgard console fails on Ruby 4 without irb in the bundle (F-32)
- [dev#9](https://github.com/MadBomber/dev/issues/9) rubocop-strict.yml promises more than RuboCop enforces (F-38)
- [dev#13](https://github.com/MadBomber/dev/issues/13) release crashes with NoMethodError when stdin is closed (F-43)
- [dev#14](https://github.com/MadBomber/dev/issues/14) quality summary KeyError on a non-status *_check (F-44)
- [dev#15](https://github.com/MadBomber/dev/issues/15) github_collect ignores RR (F-40)
- [dev#23](https://github.com/MadBomber/dev/issues/23) reek_check ignores exclude_paths and directories in .reek.yml (F-47)
- [dev#24](https://github.com/MadBomber/dev/issues/24) quality_rails.loki NoMethodError unless quality.loki is imported (F-50)
- [dev#27](https://github.com/MadBomber/dev/issues/27) README doesn't list what a consumer repo needs (F-36)
- [dev#28](https://github.com/MadBomber/dev/issues/28) status_report collectors print git/gh/icalBuddy stderr (F-52)
- [dev#29](https://github.com/MadBomber/dev/issues/29) calendar_collect says icalBuddy is missing when it is installed but fails (F-53; fix with #28 after #20)

Dewayne's own PRs cover #7 (#17), #8 (#18), #9 (#21), #13 (#16), #14 (#19), #15 (#20).

## Merged bomberbot PRs

[dev#12](https://github.com/MadBomber/dev/pull/12) skip absent flog/flay/reek/bundler-audit (F-27, 2026-11-12), [dev#11](https://github.com/MadBomber/dev/pull/11) reek_baseline task (F-26, 2026-11-13; merged with doubts, see taste), [dev#10](https://github.com/MadBomber/dev/pull/10) STATUS_WEEK_OF snaps to Monday (F-28, 2026-11-12), [dev#6](https://github.com/MadBomber/dev/pull/6) quality gates skip unbundled Rails/fasterer (2026-11-09), [dev#5](https://github.com/MadBomber/dev/pull/5) status_report survives missing base64/claude (F-24, 2026-11-09), [dev#4](https://github.com/MadBomber/dev/pull/4) branch_report vs origin/<default> (F-11, 2026-11-07), [dev#3](https://github.com/MadBomber/dev/pull/3) (2026-11-07), [dev#2](https://github.com/MadBomber/dev/pull/2) (2026-11-07), [dev#1](https://github.com/MadBomber/dev/pull/1) (2026-11-07).
