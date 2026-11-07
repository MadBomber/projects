# kbs

Knowledge-based system gem, idle since early 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/kbs
- RubyGems: https://rubygems.org/gems/kbs
- Issues: https://github.com/MadBomber/kbs/issues

## Status

_Updated 2026-11-07._ [#6](https://github.com/MadBomber/kbs/pull/6) and [#7](https://github.com/MadBomber/kbs/pull/7) merged; [#8](https://github.com/MadBomber/kbs/pull/8) (**PR open**) adds the repo's first test CI (Ruby 3.2 to 4.0 plus a Redis service container, all green).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make the Redis test URL configurable and skip the tests by default, so `rake` never flushes a developer's live Redis (findings F-2 and F-3). [#7](https://github.com/MadBomber/kbs/pull/7) (merged 2026-11-07)
- Add a test CI workflow once #7 lands (finding F-8). [#8](https://github.com/MadBomber/kbs/pull/8) (**PR open**)

## Open bomberbot PRs

- [#8](https://github.com/MadBomber/kbs/pull/8) ci: run the test suite on pushes and pull requests (**PR open**)
- [#7](https://github.com/MadBomber/kbs/pull/7) fix(test): run the Redis tests only when KBS_TEST_REDIS_URL is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/kbs/pull/6) fix(test): make rake test actually run the suite (merged 2026-11-07)
