# kbs

Knowledge-based system gem, idle since early 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/kbs
- RubyGems: https://rubygems.org/gems/kbs
- Issues: https://github.com/MadBomber/kbs/issues

## Status

_Updated 2026-11-06._ The suite passes on Ruby 4.1.0-preview1 and 4.0.9 (220 runs), but only when invoked directly: `rake test` ran zero tests until [#6](https://github.com/MadBomber/kbs/pull/6) (**PR open**).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make the Redis test URL configurable and skip the tests by default, so `rake` never flushes a developer's live Redis (findings F-2 and F-3).

## Open bomberbot PRs

- [#6](https://github.com/MadBomber/kbs/pull/6) fix(test): make rake test actually run the suite (**PR open**)
