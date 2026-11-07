# asgard

Thor-based task runner driven by .loki files.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-07 (second run)._ PR #8 removes the unreachable `Ractor#take` fallback in `Watch::Daemon.join_worker` (F-8). F-7 didn't reproduce against the real `ps` and is narrowed to F-9 (unverified). CI on develop is green.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-8: remove the unreachable `ractor.take` fallback in `Watch::Daemon.join_worker`; Ruby 4.0 has no `Ractor#take`. **PR open** ([#8](https://github.com/MadBomber/asgard/pull/8))

## Open bomberbot PRs

- [#8](https://github.com/MadBomber/asgard/pull/8) refactor(watch): drop the Ractor#take fallback in join_worker (**PR open**, 2026-11-07)

## Merged bomberbot PRs

- [#7](https://github.com/MadBomber/asgard/pull/7) fix(test): pass the suite when no locale is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/asgard/pull/6) Create schedule log directories owner-only (0700) (merged 2026-11-07)
