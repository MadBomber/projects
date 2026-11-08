# debug_me

Debug output gem, small and stable.

## Links

- GitHub: https://github.com/MadBomber/debug_me
- RubyGems: https://rubygems.org/gems/debug_me
- Issues: https://github.com/MadBomber/debug_me/issues

## Status

_Updated 2026-11-08._ #18 merged; PR [#19](https://github.com/MadBomber/debug_me/pull/19) drops the unused amazing_print dev dependency and a Ruby 1.8.6 branch (issue #17). **PR open**

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#19](https://github.com/MadBomber/debug_me/pull/19) chore: drop unused amazing_print and a 1.8.6 branch (issue #17, findings #21). **PR open**

## Merged bomberbot PRs

- [#8](https://github.com/MadBomber/debug_me/pull/8) Accept options after a String tag (findings #1). Merged 2026-11-07.
- [#9](https://github.com/MadBomber/debug_me/pull/9) Add a rake test task and run the suite in CI (findings #8). Merged 2026-11-07.
- [#10](https://github.com/MadBomber/debug_me/pull/10) docs: fix the no-include examples and two constants (findings #2, #3, #4). Merged 2026-11-07.
- [#11](https://github.com/MadBomber/debug_me/pull/11) fix: print the levels backtrace once when the block lists :backtrace (findings #13). Merged 2026-11-07.
- [#15](https://github.com/MadBomber/debug_me/pull/15) fix: no uninitialized $DEBUG_ME warning on require. Merged 2026-11-08.
- [#18](https://github.com/MadBomber/debug_me/pull/18) fix: accept a String levels: value (issue #13, findings #14). Merged 2026-11-08.

## Open bomberbot issues

- [#12](https://github.com/MadBomber/debug_me/issues/12) README examples raise or don't parse (findings #6).
- [#13](https://github.com/MadBomber/debug_me/issues/13) A String levels: value raises (findings #14). Fixed on develop by #18; stays open until a release.
- [#14](https://github.com/MadBomber/debug_me/issues/14) Test suite fails with DEBUG_ME=false (findings #19).
- [#16](https://github.com/MadBomber/debug_me/issues/16) `debug_me {}` shows no class variables or constants inside a class method (findings #20).
- [#17](https://github.com/MadBomber/debug_me/issues/17) Unused amazing_print test dependency and Ruby 1.8.6 caller branch (findings #21). PR #19.
- [#20](https://github.com/MadBomber/debug_me/issues/20) README says `DebugMe::debug_me(){}` works, but it raises (findings #24).
