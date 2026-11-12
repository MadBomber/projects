# dev

Shared Asgard task libraries (.loki) for madbomber repos. Not a gem: its suite is whatever `test/` holds plus a clean load of every `.loki` file.

## Links

- GitHub: https://github.com/MadBomber/dev
- RubyGems: https://rubygems.org/gems/dev
- Issues: https://github.com/MadBomber/dev/issues

## Status

_Updated 2026-11-12 (fourth run)._ Dewayne merged dev#4–#6; opened dev#10 (weekly_report week snap, F-28) and dev#11 (reek_baseline task, F-26), with SKIP-for-absent-tools (F-27) stacked on #11; filed issues #7–#9. `develop` (5a0b89e) is 26 commits ahead of `main`.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-5: drop the header comments copied from the vbr project (after F-6 is decided).
- F-37: keep git `fatal:` noise out of `branch_report` stderr (1 line).
- F-13: escape `|` in branch_report author cells.
- F-39: stale header comments and labels in quality.loki.

## Open bomberbot PRs

- [dev#10](https://github.com/MadBomber/dev/pull/10): fix(status_report): snap STATUS_WEEK_OF back to Monday (F-28). **PR open**
- [dev#11](https://github.com/MadBomber/dev/pull/11): feat(quality): add reek_baseline task (F-26). **PR open**

## Open bomberbot issues

- [dev#7](https://github.com/MadBomber/dev/issues/7) gem_version can read the wrong version.rb (F-31)
- [dev#8](https://github.com/MadBomber/dev/issues/8) asgard console fails on Ruby 4 without irb in the bundle (F-32)
- [dev#9](https://github.com/MadBomber/dev/issues/9) rubocop-strict.yml promises more than RuboCop enforces (F-38)

## Merged bomberbot PRs

[dev#6](https://github.com/MadBomber/dev/pull/6) quality gates skip unbundled Rails/fasterer (2026-11-09), [dev#5](https://github.com/MadBomber/dev/pull/5) status_report survives missing base64/claude (F-24, 2026-11-09), [dev#4](https://github.com/MadBomber/dev/pull/4) branch_report vs origin/<default> (F-11, 2026-11-07), [dev#3](https://github.com/MadBomber/dev/pull/3) (2026-11-07), [dev#2](https://github.com/MadBomber/dev/pull/2) (2026-11-07), [dev#1](https://github.com/MadBomber/dev/pull/1) (2026-11-07).
