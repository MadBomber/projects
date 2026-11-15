# kbs

Knowledge-based system gem, idle since early 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/kbs
- RubyGems: https://rubygems.org/gems/kbs
- Issues: https://github.com/MadBomber/kbs/issues

## Status

_Updated 2026-11-15 (second visit)._ No approvals since the first visit; #53 and #55 rebased and green. This visit opened [#56](https://github.com/MadBomber/kbs/pull/56) (ternary decompile, #52), [#61](https://github.com/MadBomber/kbs/pull/61) (Ruby 4.1 strings, #50, stacked on #56) and [#57](https://github.com/MadBomber/kbs/pull/57) (`clear_session`, #51), ran the security audit, and filed #58 (`print_rules` Procs), #59 (docs `?var` prefix) and #60 (queue `consume` race).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make the Redis test URL configurable and skip the tests by default, so `rake` never flushes a developer's live Redis (findings F-2 and F-3). [#7](https://github.com/MadBomber/kbs/pull/7) (merged 2026-11-07)
- Add a test CI workflow once #7 lands (finding F-8). [#8](https://github.com/MadBomber/kbs/pull/8) (merged 2026-11-07)
- Load the SQLite blackboard without the redis gem (finding F-4). [#9](https://github.com/MadBomber/kbs/pull/9) (merged 2026-11-07)
- Bind `?variables` to the right facts when a `without` precedes positive conditions (finding F-23). [#13](https://github.com/MadBomber/kbs/pull/13) (merged 2026-11-08)
- Correct the README Testing and Documentation sections (issue #12, F-17). [#18](https://github.com/MadBomber/kbs/pull/18) (merged 2026-11-08)
- Bring Copilot's `main`-only fixes (#19-#27) to `develop` so the release merge is clean. [#28](https://github.com/MadBomber/kbs/pull/28) (merged 2026-11-12)

- Rule `priority:` keyword (issue #29). [#37](https://github.com/MadBomber/kbs/pull/37) (merged 2026-11-13)
- Ship only lib/ and top-level docs in the gem (issue #31). [#39](https://github.com/MadBomber/kbs/pull/39) (merged 2026-11-13; CHANGELOG line [#54](https://github.com/MadBomber/kbs/pull/54), merged as low risk 2026-11-15)
- A rule added after facts exist makes earlier rules fire twice (F-41, issue [#34](https://github.com/MadBomber/kbs/issues/34)). Fixed by Dewayne in #44 (2026-11-13).
- Fire rules in priority order (F-42, issue [#35](https://github.com/MadBomber/kbs/issues/35)); also fix `docs/api/rules.md:154-155` and `docs/advanced/testing.md:378-399`. Code fixed by Dewayne in #45 (2026-11-13); remaining docs [#55](https://github.com/MadBomber/kbs/pull/55) (**PR open**).
- Decompiler `&&`/`||` (F-38, issue [#36](https://github.com/MadBomber/kbs/issues/36)); the improver has a prototype approach in the issue. Fixed by Dewayne in #46 (2026-11-13).
- `Rule#to_s` raises on an undecompilable condition such as `:even?.to_proc` (F-39, first half): rescue it like the action path. [#43](https://github.com/MadBomber/kbs/pull/43) (merged 2026-11-13)
- Drop dead code: `Blackboard::Engine#update` duplicate, `BetaMemory` dead `unlink!`, `@current_condition_group` (F-45, F-52, F-48 first half). [#53](https://github.com/MadBomber/kbs/pull/53) (**PR open**)
- Ruby 4.1 decompiler renders string literals as placeholders (F-60, issue [#50](https://github.com/MadBomber/kbs/issues/50)). [#61](https://github.com/MadBomber/kbs/pull/61) (**PR open**, stacks on #56)
- `clear_session` clears nothing with a passed-in store (F-82, issue [#51](https://github.com/MadBomber/kbs/issues/51)). [#57](https://github.com/MadBomber/kbs/pull/57) (**PR open**)
- Ternary decompile drops the condition (F-61, issue [#52](https://github.com/MadBomber/kbs/issues/52)). [#56](https://github.com/MadBomber/kbs/pull/56) (**PR open**)
- Small PR candidates: `gem "benchmark"` for Ruby 4 (F-62), CSV examples from repo root (F-80), `query_facts` docs pass strings (F-81), `prioritized_production_nodes` private (F-66), `test.yml` action bumps (F-64).
- `print_rules` shows helper conditions as raw Procs (F-90, issue [#58](https://github.com/MadBomber/kbs/issues/58)).
- Docs say variables start with `?` (F-92, issue [#59](https://github.com/MadBomber/kbs/issues/59)); hold the `rete-algorithm.md` line until #55 merges.
- Decompiler crashes on a block inside a block (F-94).

## Open bomberbot PRs

- [#53](https://github.com/MadBomber/kbs/pull/53) refactor: drop dead code in Blackboard::Engine, BetaMemory, RuleBuilder (**PR open**, F-45/F-52/F-48)
- [#55](https://github.com/MadBomber/kbs/pull/55) docs: describe priority-ordered firing for both engines (**PR open**, Refs #35)
- [#56](https://github.com/MadBomber/kbs/pull/56) fix(decompiler): ternary branches (**PR open**, Fixes #52)
- [#57](https://github.com/MadBomber/kbs/pull/57) fix(blackboard): clear_session with a passed-in store (**PR open**, Fixes #51)
- [#61](https://github.com/MadBomber/kbs/pull/61) fix(decompiler): render Ruby 4.1 dupchilledstring (**PR open**, Fixes #50, stacks on #56)

## Merged bomberbot PRs

- [#54](https://github.com/MadBomber/kbs/pull/54) docs(changelog): note the gem-contents and decompiler fixes (merged as low risk 2026-11-15, develop 70c52ca)
- [#43](https://github.com/MadBomber/kbs/pull/43) fix(rule): fall back when a condition proc cannot be decompiled (merged 2026-11-13)
- [#39](https://github.com/MadBomber/kbs/pull/39) fix(gemspec): package only lib/ and top-level docs (merged 2026-11-13)
- [#37](https://github.com/MadBomber/kbs/pull/37) fix(dsl): accept priority: keyword in rule and defrule (merged 2026-11-13)
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
