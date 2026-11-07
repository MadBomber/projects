# asgard

Thor-based task runner driven by .loki files.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-07._ PR #7 makes the suite pass when no locale is set, as under launchd and cron (finding F-2). Ran a dependencies audit (clean), which found a lib locale crash in `Pid` (F-7). CI on develop is green.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-7: `Asgard::Pid` raises on non-ASCII `ps` output when asgard runs with no locale (launchd/cron). Tag that output UTF-8 and scrub it inside `Pid`. See `bomberbot/findings/asgard.md`.
- F-8: remove the unreachable `ractor.take` fallback in `Watch::Daemon.join_worker`; Ruby 4.0 has no `Ractor#take`.

## Open bomberbot PRs

- [#7](https://github.com/MadBomber/asgard/pull/7) fix(test): pass the suite when no locale is set (**PR open**, 2026-11-07)

## Merged bomberbot PRs

- [#6](https://github.com/MadBomber/asgard/pull/6) Create schedule log directories owner-only (0700) (merged 2026-11-07)
