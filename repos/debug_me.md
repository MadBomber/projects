# debug_me

Debug output gem, small and stable.

## Links

- GitHub: https://github.com/MadBomber/debug_me
- RubyGems: https://rubygems.org/gems/debug_me
- Issues: https://github.com/MadBomber/debug_me/issues

## Status

_Updated 2026-11-09 02:40 (resumed visit)._ #22, #23, #24 and #36 merged to `develop`; [#37](https://github.com/MadBomber/debug_me/pull/37) open (harden env_test subprocess tests, findings #12/#23). **The main/develop split widened:** Copilot shipped parallel fixes to `main` (PRs #29–#35, merged by Dewayne) that closed issues #12, #14, #16, #25, #26, #27 there, while `develop` carries bomberbot's own fixes for the same ground (#8–#24). Two Copilot PRs are still open against `main` (#28 string levels = issue #13, #32 remove amazing_print = issue #17). Only issues #13 and #17 remain open, both already fixed on `develop`. The sync is Dewayne's to do (findings #30, inbox, dewayne.md). **PR open**

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#37](https://github.com/MadBomber/debug_me/pull/37) test: harden env_test subprocess tests against env leaks (findings #12, #23). **PR open**

## Merged bomberbot PRs

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

- [#13](https://github.com/MadBomber/debug_me/issues/13) A String levels: value raises (findings #14). Fixed on develop by #18; stays open until a release. BomberBot commented 2026-11-08 (develop fix + Copilot PR #28 on main).
- [#17](https://github.com/MadBomber/debug_me/issues/17) Unused amazing_print test dependency and Ruby 1.8.6 caller branch (findings #21). Both items fixed on develop (#19, #23); stays open until a release. BomberBot commented 2026-11-08 (Copilot PR #32 covers it on main).

### Closed since last visit (by Copilot PRs on `main`, merged by Dewayne)

- [#12](https://github.com/MadBomber/debug_me/issues/12) README examples (findings #6) — closed by #29.
- [#14](https://github.com/MadBomber/debug_me/issues/14) suite fails with DEBUG_ME=false (findings #19) — closed by #30. (bomberbot #24 fixed the same on develop.)
- [#16](https://github.com/MadBomber/debug_me/issues/16) no class vars/consts in a class method (findings #20) — closed by #31.
- [#20](https://github.com/MadBomber/debug_me/issues/20) README `DebugMe::debug_me(){}` (findings #24) — closed by #21.
- [#25](https://github.com/MadBomber/debug_me/issues/25) gem package ships unused files (findings #9, #23) — closed by #33.
- [#26](https://github.com/MadBomber/debug_me/issues/26) whole call stack on every call (findings #26) — closed by #34.
- [#27](https://github.com/MadBomber/debug_me/issues/27) stale config/API docs (findings #15, #16) — closed by #35.
