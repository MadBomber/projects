# debug_me

Debug output gem, small and stable.

## Links

- GitHub: https://github.com/MadBomber/debug_me
- RubyGems: https://rubygems.org/gems/debug_me
- Issues: https://github.com/MadBomber/debug_me/issues

## Status

_Updated 2026-11-12 02:30._ New PR [#48](https://github.com/MadBomber/debug_me/pull/48) makes `skip1:`/`skip2:` work with `header: false` (touches lib, awaits Dewayne). Merged as low risk: [#47](https://github.com/MadBomber/debug_me/pull/47) (api-reference backtrace format) and [#49](https://github.com/MadBomber/debug_me/pull/49) (suite green under `DEBUG_ME=false`). **The main/develop split is over:** develop now contains all of main (64 ahead, 0 behind), with Copilot's fixes cherry-picked; findings #30 is settled. Filed issues [#44](https://github.com/MadBomber/debug_me/issues/44), [#45](https://github.com/MadBomber/debug_me/issues/45), [#46](https://github.com/MadBomber/debug_me/issues/46). Only #39 (fixed on develop by #43) and the new issues are open. **PR open**

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#48](https://github.com/MadBomber/debug_me/pull/48) fix: skip1/skip2 add blank lines when header is off (triage; docs promised it). **PR open**

## Merged bomberbot PRs

- [#49](https://github.com/MadBomber/debug_me/pull/49) test: keep the whole suite green under DEBUG_ME=false. **Merged (low risk)** by madbot 2026-11-12 → develop 200c10f.
- [#47](https://github.com/MadBomber/debug_me/pull/47) docs: distinguish levels: pairs from :backtrace frame strings. **Merged (low risk)** by madbot 2026-11-12 → develop b5d5323.
- [#43](https://github.com/MadBomber/debug_me/pull/43) chore: add documentation_uri and https homepage to gemspec (issue #39). Merged 2026-11-10 (rebase) → 0d7fb14.
- [#41](https://github.com/MadBomber/debug_me/pull/41) docs: correct api-reference and configuration sample output. Merged (low risk, by madbot) 2026-11-10.
- [#40](https://github.com/MadBomber/debug_me/pull/40) test: run gemspec packaging check in a clean bundler env. Merged (low risk, by madbot) 2026-11-10. (#42 duplicated it and was closed.)
- [#38](https://github.com/MadBomber/debug_me/pull/38) docs: correct sample output to match real debug_me output. Merged 2026-11-09.
- [#37](https://github.com/MadBomber/debug_me/pull/37) test: harden env_test subprocess tests against env leaks. Merged 2026-11-09.
- [#22](https://github.com/MadBomber/debug_me/pull/22) fix: print non-String block values instead of evaluating them (findings #28). Merged 2026-11-08.
- [#23](https://github.com/MadBomber/debug_me/pull/23) chore: raise the minimum Ruby to 3.2 (issue #17). Merged 2026-11-08.
- [#24](https://github.com/MadBomber/debug_me/pull/24) test: keep the suite green with DEBUG_ME=false (issue #14, findings #19). Merged 2026-11-08.
- [#36](https://github.com/MadBomber/debug_me/pull/36) test: remove the unused WorldView#everything helper (improver C1). Merged 2026-11-08.

- [#8](https://github.com/MadBomber/debug_me/pull/8) Accept options after a String tag (findings #1). Merged 2026-11-07.
- [#9](https://github.com/MadBomber/debug_me/pull/9) Add a rake test task and run the suite in CI (findings #8). Merged 2026-11-07.
- [#10](https://github.com/MadBomber/debug_me/pull/10) docs: fix the no-include examples and two constants (findings #2, #3, #4). Merged 2026-11-07.
- [#11](https://github.com/MadBomber/debug_me/pull/11) fix: print the levels backtrace once when the block lists :backtrace (findings #13). Merged 2026-11-07.
- [#15](https://github.com/MadBomber/debug_me/pull/15) fix: no uninitialized $DEBUG_ME warning on require. Merged 2026-11-08.
- [#18](https://github.com/MadBomber/debug_me/pull/18) fix: accept a String levels: value (issue #13, findings #14). Merged 2026-11-08.
- [#19](https://github.com/MadBomber/debug_me/pull/19) chore: drop unused amazing_print and a 1.8.6 branch (issue #17, findings #21). Merged 2026-11-08.

## Open bomberbot issues

- [#44](https://github.com/MadBomber/debug_me/issues/44) Empty-block discovery reads constants lexically, not from the receiver's class (findings #38). Filed 2026-11-12.
- [#45](https://github.com/MadBomber/debug_me/issues/45) `debug_me {}` fails or shows the wrong locals when self is a BasicObject or Delegator (findings #39). Filed 2026-11-12.
- [#46](https://github.com/MadBomber/debug_me/issues/46) CI actions target the deprecated Node.js 20 runtime (findings #40). Filed 2026-11-12.
- [#39](https://github.com/MadBomber/debug_me/issues/39) gemspec omits `documentation_uri` and uses an http:// homepage (findings #37). **Fixed on develop by #43** (2026-11-10); stays open until a release reaches main.

### Closed since last visit (by Copilot PRs on `main`, merged by Dewayne)

- [#13](https://github.com/MadBomber/debug_me/issues/13) A String levels: value raises — closed on main by Copilot #28/#32 (2026-11-10).
- [#17](https://github.com/MadBomber/debug_me/issues/17) Unused amazing_print test dependency and Ruby 1.8.6 caller branch — closed on main by Copilot #28/#32 (2026-11-10).

- [#12](https://github.com/MadBomber/debug_me/issues/12) README examples (findings #6) — closed by #29.
- [#14](https://github.com/MadBomber/debug_me/issues/14) suite fails with DEBUG_ME=false (findings #19) — closed by #30. (bomberbot #24 fixed the same on develop.)
- [#16](https://github.com/MadBomber/debug_me/issues/16) no class vars/consts in a class method (findings #20) — closed by #31.
- [#20](https://github.com/MadBomber/debug_me/issues/20) README `DebugMe::debug_me(){}` (findings #24) — closed by #21.
- [#25](https://github.com/MadBomber/debug_me/issues/25) gem package ships unused files (findings #9, #23) — closed by #33.
- [#26](https://github.com/MadBomber/debug_me/issues/26) whole call stack on every call (findings #26) — closed by #34.
- [#27](https://github.com/MadBomber/debug_me/issues/27) stale config/API docs (findings #15, #16) — closed by #35.
