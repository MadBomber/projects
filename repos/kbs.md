# kbs

Knowledge-based system gem, idle since early 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/kbs
- RubyGems: https://rubygems.org/gems/kbs
- Issues: https://github.com/MadBomber/kbs/issues

## Status

_Updated 2026-11-12._ #17 and #18 merged. Dewayne merged Copilot's #19-#27 straight into `main`, so `main` and `develop` diverged (7 conflicting files); [#28](https://github.com/MadBomber/kbs/pull/28) (**PR open**) merges `main` into `develop` with the conflicts resolved. [#32](https://github.com/MadBomber/kbs/pull/32) and [#33](https://github.com/MadBomber/kbs/pull/33) (**PR open**) fix the CSV examples under Bundler and the decompiler's captured values. Issues #29 to #31 track `rule priority:`, the README AI example and gem packaging.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make the Redis test URL configurable and skip the tests by default, so `rake` never flushes a developer's live Redis (findings F-2 and F-3). [#7](https://github.com/MadBomber/kbs/pull/7) (merged 2026-11-07)
- Add a test CI workflow once #7 lands (finding F-8). [#8](https://github.com/MadBomber/kbs/pull/8) (merged 2026-11-07)
- Load the SQLite blackboard without the redis gem (finding F-4). [#9](https://github.com/MadBomber/kbs/pull/9) (merged 2026-11-07)
- Bind `?variables` to the right facts when a `without` precedes positive conditions (finding F-23). [#13](https://github.com/MadBomber/kbs/pull/13) (merged 2026-11-08)
- Correct the README Testing and Documentation sections (issue #12, F-17). [#18](https://github.com/MadBomber/kbs/pull/18) (merged 2026-11-08)
- Bring Copilot's `main`-only fixes (#19-#27) to `develop` so the release merge is clean. [#28](https://github.com/MadBomber/kbs/pull/28) (**PR open**, merge with a merge commit)

## Open bomberbot PRs

- [#28](https://github.com/MadBomber/kbs/pull/28) Merge main into develop (sync #19-#27) (**PR open**, needs-dewayne)
- [#32](https://github.com/MadBomber/kbs/pull/32) fix(examples): add csv to Gemfile for Ruby 3.4+ (**PR open**)
- [#33](https://github.com/MadBomber/kbs/pull/33) fix(decompiler): render values captured by condition helpers (**PR open**)

## Merged bomberbot PRs

- [#18](https://github.com/MadBomber/kbs/pull/18) docs: correct the README Testing and Documentation sections (merged 2026-11-08)
- [#17](https://github.com/MadBomber/kbs/pull/17) fix: accept an engine: keyword in KBS.knowledge_base (merged 2026-11-08)
- [#13](https://github.com/MadBomber/kbs/pull/13) fix: bind variables correctly after a negated condition (merged 2026-11-08)
- [#9](https://github.com/MadBomber/kbs/pull/9) fix(blackboard): load the SQLite blackboard without the redis gem (merged 2026-11-07)
- [#8](https://github.com/MadBomber/kbs/pull/8) ci: run the test suite on pushes and pull requests (merged 2026-11-07)
- [#7](https://github.com/MadBomber/kbs/pull/7) fix(test): run the Redis tests only when KBS_TEST_REDIS_URL is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/kbs/pull/6) fix(test): make rake test actually run the suite (merged 2026-11-07)
