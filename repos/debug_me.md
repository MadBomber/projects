# debug_me

Debug output gem, small and stable.

## Links

- GitHub: https://github.com/MadBomber/debug_me
- RubyGems: https://rubygems.org/gems/debug_me
- Issues: https://github.com/MadBomber/debug_me/issues

## Status

_Updated 2026-11-08 13:30._ #19 merged. PRs [#22](https://github.com/MadBomber/debug_me/pull/22) (print non-String block values, findings #28) and [#23](https://github.com/MadBomber/debug_me/pull/23) (minimum Ruby 3.2, issue #17) and [#24](https://github.com/MadBomber/debug_me/pull/24) (suite green with DEBUG_ME=false, issue #14). Copilot PR #21 (fixes #20) merged to `main`, not `develop`: the branches have diverged (findings #30, in the inbox). **PR open**

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#22](https://github.com/MadBomber/debug_me/pull/22) fix: print non-String block values instead of evaluating them (findings #28). **PR open**
- [#23](https://github.com/MadBomber/debug_me/pull/23) chore: raise the minimum Ruby to 3.2 (issue #17, Dewayne's comment). **PR open**
- [#24](https://github.com/MadBomber/debug_me/pull/24) test: keep the suite green with DEBUG_ME=false (issue #14, findings #19). **PR open**

## Merged bomberbot PRs

- [#8](https://github.com/MadBomber/debug_me/pull/8) Accept options after a String tag (findings #1). Merged 2026-11-07.
- [#9](https://github.com/MadBomber/debug_me/pull/9) Add a rake test task and run the suite in CI (findings #8). Merged 2026-11-07.
- [#10](https://github.com/MadBomber/debug_me/pull/10) docs: fix the no-include examples and two constants (findings #2, #3, #4). Merged 2026-11-07.
- [#11](https://github.com/MadBomber/debug_me/pull/11) fix: print the levels backtrace once when the block lists :backtrace (findings #13). Merged 2026-11-07.
- [#15](https://github.com/MadBomber/debug_me/pull/15) fix: no uninitialized $DEBUG_ME warning on require. Merged 2026-11-08.
- [#18](https://github.com/MadBomber/debug_me/pull/18) fix: accept a String levels: value (issue #13, findings #14). Merged 2026-11-08.
- [#19](https://github.com/MadBomber/debug_me/pull/19) chore: drop unused amazing_print and a 1.8.6 branch (issue #17, findings #21). Merged 2026-11-08.

## Open bomberbot issues

- [#12](https://github.com/MadBomber/debug_me/issues/12) README examples raise or don't parse (findings #6).
- [#13](https://github.com/MadBomber/debug_me/issues/13) A String levels: value raises (findings #14). Fixed on develop by #18; stays open until a release.
- [#14](https://github.com/MadBomber/debug_me/issues/14) Test suite fails with DEBUG_ME=false (findings #19). PR #24.
- [#16](https://github.com/MadBomber/debug_me/issues/16) `debug_me {}` shows no class variables or constants inside a class method (findings #20). Still reproduces on `main` after #21; Dewayne approved the fix; waits for the main/develop sync.
- [#17](https://github.com/MadBomber/debug_me/issues/17) Unused amazing_print test dependency and Ruby 1.8.6 caller branch (findings #21). #19 merged; Dewayne asked to drop old Rubies too: PR #23.
- [#20](https://github.com/MadBomber/debug_me/issues/20) README says `DebugMe::debug_me(){}` works (findings #24). Closed by Copilot PR #21 on `main`; `develop` still raises until the branches are synced.
- [#25](https://github.com/MadBomber/debug_me/issues/25) The gem package ships docs, tests and repo files it never uses (findings #9, #23).
- [#26](https://github.com/MadBomber/debug_me/issues/26) debug_me builds the whole call stack on every call (findings #26).
- [#27](https://github.com/MadBomber/debug_me/issues/27) configuration and API reference docs are out of date (findings #15, #16).
