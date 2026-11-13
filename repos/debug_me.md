# debug_me

Debug output gem, small and stable.

## Links

- GitHub: https://github.com/MadBomber/debug_me
- RubyGems: https://rubygems.org/gems/debug_me
- Issues: https://github.com/MadBomber/debug_me/issues

## Status

_Updated 2026-11-13 08:45._ Second visit of the day; Dewayne hasn't touched the repo since 03:36, so #61 and #63 still await him. This visit opened [#64](https://github.com/MadBomber/debug_me/pull/64) (logger guard test), [#65](https://github.com/MadBomber/debug_me/pull/65) (CHANGELOG `[Unreleased]` tidy, fixes #60) and [#66](https://github.com/MadBomber/debug_me/pull/66) (skip DebugMe's own constants, fixes #59), none self-merged, and filed [#67](https://github.com/MadBomber/debug_me/issues/67). Five bomberbot PRs now await review, so the next visit is tend-only. `develop` (fb0b2ea) is ~74 commits ahead of `main`; the live docs site stays stale until a release (findings #53). **PR open**

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Shared `$DEBUG_ME` on/off test helper: the same save/force/restore before/after block is now copied across five spec files (noticed by the #61 builder). Test-only; do it after #61 and #63 merge.

## Open bomberbot PRs

- [#61](https://github.com/MadBomber/debug_me/pull/61) test: cover the time and strftime options (findings #70). Tests only, CI green, review hit the cap → awaits Dewayne. **PR open**
- [#63](https://github.com/MadBomber/debug_me/pull/63) fix: print BasicObject values instead of raising (findings #64). lib/, CI green, review hit the cap → awaits Dewayne. **PR open**
- [#64](https://github.com/MadBomber/debug_me/pull/64) test: pin that a logger without #debug is skipped (improver mutation). Tests only (new file), CI green; review round 1 deferred two lib ideas → not low risk, awaits Dewayne. **PR open**
- [#65](https://github.com/MadBomber/debug_me/pull/65) docs: tidy the Unreleased CHANGELOG notes (Fixes #60, findings #73). CHANGELOG + README, CI green; review rejected two items as out of scope → not low risk, awaits Dewayne. **PR open**
- [#66](https://github.com/MadBomber/debug_me/pull/66) fix: skip DebugMe's own constants in empty-block discovery (Fixes #59). lib/, adds `DebugMe.own_constant?`, CI green, review hit the cap → awaits Dewayne. Its CHANGELOG line will need a rebase after #65. **PR open**

## Merged bomberbot PRs

- [#62](https://github.com/MadBomber/debug_me/pull/62) test: read every assigned local so ruby -w stays quiet (findings #25, rest of #53). **Merged (low risk)** by bomberbot 2026-11-13 → develop fb0b2ea.
- [#52](https://github.com/MadBomber/debug_me/pull/52) fix: read discovered names from the block's receiver (issues #44, #45). Merged by Dewayne 2026-11-12 (rebase) → e979bdb.
- [#51](https://github.com/MadBomber/debug_me/pull/51) test: make the disabled $DEBUG_ME test catch output. Merged by Dewayne 2026-11-12 → 1aba775.
- [#48](https://github.com/MadBomber/debug_me/pull/48) fix: skip1/skip2 add blank lines when header is off. Merged by Dewayne 2026-11-12 → e2ce9d8.
- [#50](https://github.com/MadBomber/debug_me/pull/50) docs: correct README examples and install fences (findings #46, #54, #55). **Merged (low risk)** by madbot 2026-11-12 → develop 8fbbbde.
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

- [#58](https://github.com/MadBomber/debug_me/issues/58) A local named `backtrace` shows the call stack in an empty-block dump (findings #65). Filed 2026-11-13.
- [#59](https://github.com/MadBomber/debug_me/issues/59) Classes that include DebugMe dump `DEBUG_ME_VERSION` on every empty block (findings #69). Filed 2026-11-13. **PR open** [#66](https://github.com/MadBomber/debug_me/pull/66).
- [#60](https://github.com/MadBomber/debug_me/issues/60) CHANGELOG `[Unreleased]` misses the docs fixes and mislabels one entry (findings #50). Filed 2026-11-13. **PR open** [#65](https://github.com/MadBomber/debug_me/pull/65).
- [#67](https://github.com/MadBomber/debug_me/issues/67) The docs never say an undefined name prints `"<undefined>"` (findings #71, #72). Filed 2026-11-13 08:45.

### Closed by Dewayne on 2026-11-13 (fixed on develop)

- #39 (by #43), #44 and #45 (by #52), #46 (his #57), #53 (his #56; #62 finished it), #54 (his #55).

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
