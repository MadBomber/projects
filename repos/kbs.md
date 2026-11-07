# kbs

Knowledge-based system gem, idle since early 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/kbs
- RubyGems: https://rubygems.org/gems/kbs
- Issues: https://github.com/MadBomber/kbs/issues

## Status

_Updated 2026-11-07._ [#6](https://github.com/MadBomber/kbs/pull/6) merged, so `rake test` runs the suite (224 runs, green on 4.1.0-preview1 and 4.0.9); [#7](https://github.com/MadBomber/kbs/pull/7) (**PR open**) makes the Redis tests opt-in via `KBS_TEST_REDIS_URL` so `rake` never flushes a live Redis.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make the Redis test URL configurable and skip the tests by default, so `rake` never flushes a developer's live Redis (findings F-2 and F-3). [#7](https://github.com/MadBomber/kbs/pull/7) (**PR open**)
- Add a test CI workflow once #7 lands (finding F-8).

## Open bomberbot PRs

- [#7](https://github.com/MadBomber/kbs/pull/7) fix(test): run the Redis tests only when KBS_TEST_REDIS_URL is set (**PR open**)
- [#6](https://github.com/MadBomber/kbs/pull/6) fix(test): make rake test actually run the suite (merged 2026-11-07)
