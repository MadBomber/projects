# debug_me

Debug output gem, small and stable.

## Links

- GitHub: https://github.com/MadBomber/debug_me
- RubyGems: https://rubygems.org/gems/debug_me
- Issues: https://github.com/MadBomber/debug_me/issues

## Status

_Updated 2026-11-15 03:40._ No approvals since the 02:45 visit; #71/#72 rebased and green. This visit merged [#79](https://github.com/MadBomber/debug_me/pull/79) as low risk (contributing guide, issue #73), opened [#77](https://github.com/MadBomber/debug_me/pull/77) (drop the `bundler` dev dependency, finding 76) and [#78](https://github.com/MadBomber/debug_me/pull/78) (lib comments, issue #74), and filed [#75](https://github.com/MadBomber/debug_me/issues/75) and [#76](https://github.com/MadBomber/debug_me/issues/76). `develop` 3aebfa5. **PR open**

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Shared `$DEBUG_ME` on/off test helper — **PR open** [#71](https://github.com/MadBomber/debug_me/pull/71).

## Open bomberbot PRs

- [#77](https://github.com/MadBomber/debug_me/pull/77) chore: drop the bundler development dependency (finding 76). Gemspec −1 line + CHANGELOG, CI green, review clean round 1; gemspec, so awaits Dewayne. **PR open**
- [#78](https://github.com/MadBomber/debug_me/pull/78) docs: make lib comments match DEBUG_ME and skip behaviour (Fixes #74). lib/debug_me.rb comments only, +3/−3, CI green, review clean round 1, merges cleanly with #72; lib/, so awaits Dewayne. **PR open**
- [#71](https://github.com/MadBomber/debug_me/pull/71) test: share one $DEBUG_ME on/off helper across the suite (brain Backlog). New `tests/debug_me_on.rb` + 8 spec files, +38/−61, CI green, review clean round 1, mutation-proven. Not self-merged: rewrites setup in 9 files (when in doubt, not low risk). **PR open**
- [#72](https://github.com/MadBomber/debug_me/pull/72) fix: ignore whitespace around the DEBUG_ME env value (finding 77). lib/ one line + env_test + README + CHANGELOG, CI green, review clean round 1 → awaits Dewayne. **PR open**

## Merged bomberbot PRs

- [#79](https://github.com/MadBomber/debug_me/pull/79) docs: match the contributing guide to the repo (issue #73). **Merged (low risk)** by bomberbot 2026-11-15 → develop 2de7b3e..3aebfa5 (rebase, 3 commits).
- [#70](https://github.com/MadBomber/debug_me/pull/70) docs: drop the duplicate #44/#45 CHANGELOG line. **Merged (low risk)** by madbot 2026-11-15 → develop 44456b6.
- [#66](https://github.com/MadBomber/debug_me/pull/66) fix: skip DebugMe's own constants in empty-block discovery (Fixes #59). Merged by Dewayne 2026-11-13 (squash, after merging develop in himself) → 81eca6d.
- [#65](https://github.com/MadBomber/debug_me/pull/65) docs: tidy the Unreleased CHANGELOG notes (Fixes #60). Merged by Dewayne 2026-11-13 (squash) → 6842157.
- [#64](https://github.com/MadBomber/debug_me/pull/64) test: pin that a logger without #debug is skipped. Merged by Dewayne 2026-11-13 (squash) → 4c366fd.
- [#63](https://github.com/MadBomber/debug_me/pull/63) fix: print BasicObject values instead of raising. Merged by Dewayne 2026-11-13 (squash) → 774eb65.
- [#61](https://github.com/MadBomber/debug_me/pull/61) test: cover the time and strftime options. Merged by Dewayne 2026-11-13 (squash) → 043aa12.
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

- [#73](https://github.com/MadBomber/debug_me/issues/73) docs/contributing.md PR template / test skeleton (findings #63). Fixed on develop by #79; stays open until Dewayne closes it (develop isn't the default branch).
- [#74](https://github.com/MadBomber/debug_me/issues/74) Comments in lib/debug_me.rb misdescribe skip1:/skip2: and the DEBUG_ME values (findings #48). Filed 2026-11-15; easiest after #72. **PR open** [#78](https://github.com/MadBomber/debug_me/pull/78).

- [#75](https://github.com/MadBomber/debug_me/issues/75) Output escapes a reassigned `$stdout` (findings #80). Filed 2026-11-15; options for Dewayne in the issue.
- [#76](https://github.com/MadBomber/debug_me/issues/76) CI matrix does not test Ruby 4.1 / head (findings #79). Filed 2026-11-15; workflow file, Dewayne pushes.

### Closed by Dewayne on 2026-11-13 (second batch)

- #58 (his #68), #59 (by #66), #60 (by #65), #67 (his #69).

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
