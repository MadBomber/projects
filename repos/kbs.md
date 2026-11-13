# kbs

Knowledge-based system gem, idle since early 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/kbs
- RubyGems: https://rubygems.org/gems/kbs
- Issues: https://github.com/MadBomber/kbs/issues

## Status

_Updated 2026-11-13._ Dewayne merged #28, #32 and #33 on 2026-11-12, so `main` and `develop` are back in sync. This visit closed out the three open issues: [#38](https://github.com/MadBomber/kbs/pull/38) (README AI example on LM Studio, #30) merged as low risk; [#37](https://github.com/MadBomber/kbs/pull/37) (`rule priority:`, #29) and [#39](https://github.com/MadBomber/kbs/pull/39) (gem file allowlist, #31) are **PR open**. The first quality audit filed #34 (late rule makes earlier rules fire twice), #35 (priority ignored when firing) and #36 (decompiler `&&`/`||`).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make the Redis test URL configurable and skip the tests by default, so `rake` never flushes a developer's live Redis (findings F-2 and F-3). [#7](https://github.com/MadBomber/kbs/pull/7) (merged 2026-11-07)
- Add a test CI workflow once #7 lands (finding F-8). [#8](https://github.com/MadBomber/kbs/pull/8) (merged 2026-11-07)
- Load the SQLite blackboard without the redis gem (finding F-4). [#9](https://github.com/MadBomber/kbs/pull/9) (merged 2026-11-07)
- Bind `?variables` to the right facts when a `without` precedes positive conditions (finding F-23). [#13](https://github.com/MadBomber/kbs/pull/13) (merged 2026-11-08)
- Correct the README Testing and Documentation sections (issue #12, F-17). [#18](https://github.com/MadBomber/kbs/pull/18) (merged 2026-11-08)
- Bring Copilot's `main`-only fixes (#19-#27) to `develop` so the release merge is clean. [#28](https://github.com/MadBomber/kbs/pull/28) (merged 2026-11-12)

- Rule `priority:` keyword (issue #29). [#37](https://github.com/MadBomber/kbs/pull/37) (**PR open**)
- Ship only lib/ and top-level docs in the gem (issue #31). [#39](https://github.com/MadBomber/kbs/pull/39) (**PR open**)
- A rule added after facts exist makes earlier rules fire twice (F-41, issue [#34](https://github.com/MadBomber/kbs/issues/34)).
- Fire rules in priority order (F-42, issue [#35](https://github.com/MadBomber/kbs/issues/35)); also fix `docs/api/rules.md:154-155` and `docs/advanced/testing.md:378-399`.
- Decompiler `&&`/`||` (F-38, issue [#36](https://github.com/MadBomber/kbs/issues/36)); the improver has a prototype approach in the issue.
- `Rule#to_s` raises on an undecompilable condition such as `:even?.to_proc` (F-39, first half): rescue it like the action path.

## Open bomberbot PRs

- [#37](https://github.com/MadBomber/kbs/pull/37) fix(dsl): accept priority: keyword in rule and defrule (**PR open**, Fixes #29)
- [#39](https://github.com/MadBomber/kbs/pull/39) fix(gemspec): package only lib/ and top-level docs (**PR open**, Fixes #31)

## Merged bomberbot PRs

- [#38](https://github.com/MadBomber/kbs/pull/38) docs(readme): run the AI-Enhanced Reasoning example against LM Studio (merged as low risk 2026-11-13, develop b52b35e)
- [#33](https://github.com/MadBomber/kbs/pull/33) fix(decompiler): render values captured by condition helpers (merged 2026-11-12)
- [#32](https://github.com/MadBomber/kbs/pull/32) fix(examples): add csv to Gemfile for Ruby 3.4+ (merged 2026-11-12)
- [#28](https://github.com/MadBomber/kbs/pull/28) Merge main into develop (sync #19-#27) (merged 2026-11-12)
- [#18](https://github.com/MadBomber/kbs/pull/18) docs: correct the README Testing and Documentation sections (merged 2026-11-08)
- [#17](https://github.com/MadBomber/kbs/pull/17) fix: accept an engine: keyword in KBS.knowledge_base (merged 2026-11-08)
- [#13](https://github.com/MadBomber/kbs/pull/13) fix: bind variables correctly after a negated condition (merged 2026-11-08)
- [#9](https://github.com/MadBomber/kbs/pull/9) fix(blackboard): load the SQLite blackboard without the redis gem (merged 2026-11-07)
- [#8](https://github.com/MadBomber/kbs/pull/8) ci: run the test suite on pushes and pull requests (merged 2026-11-07)
- [#7](https://github.com/MadBomber/kbs/pull/7) fix(test): run the Redis tests only when KBS_TEST_REDIS_URL is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/kbs/pull/6) fix(test): make rake test actually run the suite (merged 2026-11-07)
