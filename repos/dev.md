# dev

Shared Asgard task libraries (.loki) for madbomber repos. Not a gem: its suite is whatever `test/` holds plus a clean load of every `.loki` file.

## Links

- GitHub: https://github.com/MadBomber/dev
- RubyGems: https://rubygems.org/gems/dev
- Issues: https://github.com/MadBomber/dev/issues

## Status

_Updated 2026-11-15 (tenth run)._ Dewayne merged #38, #39, fixed #29 and #35–#37 himself and added aws, fake_aws, db, ticket, runbook and the `rails/`, `client/` layout; this visit opened #48, #52, #53 and filed #49–#51.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-51: status_report "today" wording. [dev#48](https://github.com/MadBomber/dev/pull/48) **PR open**
- F-30: gem_tasks.loki can't load without quality.loki. [dev#53](https://github.com/MadBomber/dev/pull/53) **PR open**
- F-58: bad `AWS_EMULATOR_PORT` breaks every command ([dev#49](https://github.com/MadBomber/dev/issues/49)); one-file fix in fake_aws.loki.
- F-57: fake_aws breaks when HOME has a space ([dev#50](https://github.com/MadBomber/dev/issues/50)); same file as F-58, so stack or combine.
- F-60: fake_aws smoke exits 0 on failure ([dev#51](https://github.com/MadBomber/dev/issues/51)).
- F-61: `rails/quality_rails.loki` imports `../quality.loki` by relative path, which double-loads quality through a symlinked `dev/` when rails/ is imported first; use #53's `__FILE__` + `Asgard.glob_escape` form (after #53 merges).
- F-5: `.irbrc` still opens with another project's path; waits on F-6.

Layout (since #44–#47): Rails-only tasks live in `rails/`, client-workflow tasks in `client/`; `dev/.loki` imports top-level files only. README and llms.txt change with every task change. Merge commits only (rebase and squash are off).

## Open bomberbot PRs

- [dev#48](https://github.com/MadBomber/dev/pull/48): fix(status_report): name the date, not "today" (F-51). **PR open**
- [dev#52](https://github.com/MadBomber/dev/pull/52): fix(quality): limit GateGuard rescue to real gates. **PR open**
- [dev#53](https://github.com/MadBomber/dev/pull/53): fix(gem_tasks): import quality.loki that build needs (F-30). **PR open**

## Open bomberbot issues

- [dev#49](https://github.com/MadBomber/dev/issues/49) a bad AWS_EMULATOR_PORT breaks every command importing dev/.loki (F-58)
- [dev#50](https://github.com/MadBomber/dev/issues/50) fake_aws exports and start break when HOME contains a space (F-57)
- [dev#51](https://github.com/MadBomber/dev/issues/51) fake_aws smoke exits 0 when its checks fail (F-60)

Closed by Dewayne's PRs: #7, #8, #9, #13, #14, #15, #23, #24, #27, #28, #29 (#32), #35 (#42), #36 (#43), #37 (#41).

## Merged bomberbot PRs

[dev#39](https://github.com/MadBomber/dev/pull/39) quality.loki stale comments (F-39/F-46/F-49, 2026-11-14), [dev#38](https://github.com/MadBomber/dev/pull/38) branch_report header (F-5 part, 2026-11-14), [dev#40](https://github.com/MadBomber/dev/pull/40) llms.txt (adoption, 2026-11-14, **merged (low risk)** at cf827ba), [dev#26](https://github.com/MadBomber/dev/pull/26) `|` in branch_report names (F-13, 2026-11-13), [dev#25](https://github.com/MadBomber/dev/pull/25) one-line abort on a bad date env var (F-45, 2026-11-13), [dev#22](https://github.com/MadBomber/dev/pull/22) branch_report quiet git (F-37, 2026-11-13), [dev#12](https://github.com/MadBomber/dev/pull/12) skip absent flog/flay/reek/bundler-audit (F-27, 2026-11-12), [dev#11](https://github.com/MadBomber/dev/pull/11) reek_baseline task (F-26, 2026-11-13; merged with doubts, see taste), [dev#10](https://github.com/MadBomber/dev/pull/10) STATUS_WEEK_OF snaps to Monday (F-28, 2026-11-12), [dev#6](https://github.com/MadBomber/dev/pull/6) quality gates skip unbundled Rails/fasterer (2026-11-09), [dev#5](https://github.com/MadBomber/dev/pull/5) status_report survives missing base64/claude (F-24, 2026-11-09), [dev#4](https://github.com/MadBomber/dev/pull/4) branch_report vs origin/<default> (F-11, 2026-11-07), [dev#3](https://github.com/MadBomber/dev/pull/3) (2026-11-07), [dev#2](https://github.com/MadBomber/dev/pull/2) (2026-11-07), [dev#1](https://github.com/MadBomber/dev/pull/1) (2026-11-07).
