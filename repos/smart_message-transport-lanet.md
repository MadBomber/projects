# smart_message-transport-lanet

A Ruby gem (0.0.1, **not published on RubyGems**) meant to give SmartMessage a LAN
peer-to-peer transport built on the `lanet` gem: node discovery, heartbeats, and
capability-based routing. `lib/` is one ~365-line transport file plus `version.rb`.
Runtime dependencies `smart_message` and `lanet` are unconstrained. The 64 tests run
entirely against stubs defined in `test/test_helper.rb`.

## Links

- GitHub: https://github.com/MadBomber/smart_message-transport-lanet
- Issues: https://github.com/MadBomber/smart_message-transport-lanet/issues
- Brain findings: [bomberbot/findings/smart_message-transport-lanet.md](../bomberbot/findings/smart_message-transport-lanet.md)

## Status

_Updated 2026-11-13 (visit 7): no activity since visit 6 (develop still 3b0abb9; #20–#29 and #32 open, unreviewed). No PR opened: every candidate touches files #20–#29 change. Ran the adoption audit (F-57…F-59), filed [#34](https://github.com/MadBomber/smart_message-transport-lanet/issues/34) (F-59). F-1 still waits on Dewayne._

## Backlog

Well-defined items a single small PR could close. **Dewayne's own PRs #20–#29 (2026-11-13, `fix/issue-*`) cover every open issue from #3 to #19; treat their files as in flight until they merge.**

- **F-1:** rebuild the transport on lanet's real API (recommended: `Lanet::Mesh`). **Needs Dewayne's decision** (inbox.md). Blocks most of the rest.
- **F-2 / [#3](https://github.com/MadBomber/smart_message-transport-lanet/issues/3):** constructor ordering. **Dewayne's PR [#29](https://github.com/MadBomber/smart_message-transport-lanet/pull/29).** Check local security note S-4 against it (done 2026-11-13; see the note). Its `Logger.new($stdout)` fallback still resolves to `SmartMessage::Logger` under the real gem (F-51), so it needs #27's line.
- **F-23 / [#10](https://github.com/MadBomber/smart_message-transport-lanet/issues/10):** Dewayne's PR [#25](https://github.com/MadBomber/smart_message-transport-lanet/pull/25).
- **F-20 / [#11](https://github.com/MadBomber/smart_message-transport-lanet/issues/11):** Dewayne's PR [#22](https://github.com/MadBomber/smart_message-transport-lanet/pull/22).
- **F-25 / [#12](https://github.com/MadBomber/smart_message-transport-lanet/issues/12):** Dewayne's PR [#24](https://github.com/MadBomber/smart_message-transport-lanet/pull/24).
- **F-30 / [#14](https://github.com/MadBomber/smart_message-transport-lanet/issues/14):** Dewayne's PR [#27](https://github.com/MadBomber/smart_message-transport-lanet/pull/27).
- **F-7 / [#15](https://github.com/MadBomber/smart_message-transport-lanet/issues/15):** Dewayne's PR [#23](https://github.com/MadBomber/smart_message-transport-lanet/pull/23).
- **F-33 / [#16](https://github.com/MadBomber/smart_message-transport-lanet/issues/16):** Dewayne's PR [#21](https://github.com/MadBomber/smart_message-transport-lanet/pull/21).
- **F-35 / [#17](https://github.com/MadBomber/smart_message-transport-lanet/issues/17):** Dewayne's PR [#26](https://github.com/MadBomber/smart_message-transport-lanet/pull/26). Since #9, a Class subscription still reaches `receive` after `unsubscribe!`; #26's test checks only `active_subscriptions`.
- **F-11 / [#18](https://github.com/MadBomber/smart_message-transport-lanet/issues/18):** Dewayne's PR [#28](https://github.com/MadBomber/smart_message-transport-lanet/pull/28).
- **F-38 / [#19](https://github.com/MadBomber/smart_message-transport-lanet/issues/19):** Dewayne's PR [#20](https://github.com/MadBomber/smart_message-transport-lanet/pull/20).
- **F-36 / [#30](https://github.com/MadBomber/smart_message-transport-lanet/issues/30):** `disconnect` kills threads without joining (50/50 alive right after). #29's `stop_services` keeps the bug. Next visit after #29 merges: `&.join(1)` in `stop_services` + test.
- **F-15 / [#31](https://github.com/MadBomber/smart_message-transport-lanet/issues/31):** RuboCop baseline. **PR open: [#32](https://github.com/MadBomber/smart_message-transport-lanet/pull/32)** (holds on each of #20–#29 individually). Follow-ups once it merges: CI runs default `rake` (dewayne.md), add `.rubocop_todo.yml` to the gemspec reject list.
- **F-21:** move to minitest 6 (`~> 6.0` plus `minitest-mock`); verified green in scratch 2026-11-13. After #22 merges (it touches `Gemfile.lock`).
- **F-28 (half):** remove dead `setup`/`teardown` in `test_lanet_concurrency.rb:6-13`. After #20/#28 merge. Deletes test code, so not a low-risk self-merge.
- **F-56 / [#33](https://github.com/MadBomber/smart_message-transport-lanet/issues/33):** `publish` reports success when every send fails. Raise when `target_nodes` is non-empty and none succeeded, plus one test. After #23/#25–#29 merge (same files).
- **F-59 / [#34](https://github.com/MadBomber/smart_message-transport-lanet/issues/34):** `LANET_*` env vars read once at require (`DEFAULT_CONFIG` constant). Build the ENV hash inside `default_options` per call, plus a test that sets ENV after require (save/restore). After #25 merges (same block).

## Open bomberbot PRs

- [#32](https://github.com/MadBomber/smart_message-transport-lanet/pull/32) `bomberbot/rubocop-baseline` → develop, "chore: add RuboCop baseline so default rake passes". Fixes #31 (F-15). `.rubocop.yml` +7, new `.rubocop_todo.yml` (214 lines). CI green, MERGEABLE, `/code-review` clean in 1 round (6 out-of-scope notes rejected). Tooling config, so not low risk; awaiting Dewayne.

## Merged bomberbot PRs

- **merged by Dewayne** [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5) `bomberbot/drop-ostruct`, "test: load suite on Ruby 4 and minitest 6". Replaces the one `OpenStruct` with a `Struct` and requires `minitest/mock`. Touches test files and CHANGELOG only, 6 lines. Review clean after 2 rounds. Not self-merged, because round 1 deferred an out-of-scope note (an unused dispatcher stub), which counts as open under the sqa#46 rule. Source: finding F-3.
- **merged by Dewayne** [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6) `bomberbot/ci-tests`, "ci: run the test suite on Ruby 3.3 and 3.4". Live CI is green. Hit the 3-round review cap, and every round-3 finding was fixed. Not low risk, because it touches `.github/`. Source: finding F-12.
- **merged by Dewayne** [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7) `bomberbot/readme-api-accuracy`, "docs(readme): match examples to smart_message 0.0.17 API" (`property`, `Class.subscribe`, `message.to`, binding the transport). README only, 30 lines, MERGEABLE. Review clean after 2 rounds, but round 1 rejected 8 transport-code issues as out of scope (now F-18, F-19, S-7), so it was not self-merged. Source: improver, F-14.
- **merged by Dewayne** [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8) `bomberbot/reconnect-after-disconnect`, "fix: allow connect after disconnect". Fixes #4 (F-6). Rebase-merged at 6491330 (~15 h after opening, no comment).
- **merged by Dewayne** [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13) `bomberbot/relock-ruby4`, "build: relock dev dependencies so the bundle installs on Ruby 4". Rebase-merged at 788e993 a minute after #8. Clears S-6 (bundle-audit clean).
- **merged by Dewayne** [#9](https://github.com/MadBomber/smart_message-transport-lanet/pull/9) `bomberbot/class-subscribe-key`, "fix: key Lanet message handlers by class name". Fixes #2 (F-5). Conflicted after #8/#13 (CHANGELOG); Dewayne asked `@copilot` to resolve, then rebase-merged at 3b0abb9.
