# smart_message-transport-lanet

A Ruby gem (0.0.1, **not published on RubyGems**) meant to give SmartMessage a LAN
peer-to-peer transport built on the `lanet` gem: node discovery, heartbeats, and
capability-based routing. `lib/` is one 367-line transport file plus `version.rb`.
Runtime dependencies `smart_message` and `lanet` are unconstrained. The 58 tests run
entirely against stubs defined in `test/test_helper.rb`.

## Links

- GitHub: https://github.com/MadBomber/smart_message-transport-lanet
- Issues: https://github.com/MadBomber/smart_message-transport-lanet/issues
- Brain findings: [bomberbot/findings/smart_message-transport-lanet.md](../bomberbot/findings/smart_message-transport-lanet.md)

## Status

_Updated 2026-11-12 (visit 3): skipped new work because the review queue is full (#8, #9, #13 open, green, MERGEABLE, unreviewed). Ran the adoption audit (F-30…F-34) and filed issues #14–#16. #8 and #9 will conflict on one CHANGELOG line once either merges. F-1 still waits on Dewayne._

## Backlog

Well-defined items a single small PR could close.

- **F-1:** rebuild the transport on lanet's real API (recommended: `Lanet::Mesh`). **Needs Dewayne's decision** (inbox.md). Blocks most of the rest.
- **F-5 / [#2](https://github.com/MadBomber/smart_message-transport-lanet/issues/2):** a subscription made with a Class never receives messages. **PR open [#9](https://github.com/MadBomber/smart_message-transport-lanet/pull/9).**
- **F-6 / [#4](https://github.com/MadBomber/smart_message-transport-lanet/issues/4):** the transport can't reconnect after `disconnect`. **PR open [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8).**
- **F-16:** relock `Gemfile.lock` so the dev bundle installs on Ruby 4. **PR open [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13)** (also adds Ruby 4.0 to CI, F-22).
- **F-2 / [#3](https://github.com/MadBomber/smart_message-transport-lanet/issues/3):** constructor ordering against the real `Base`; threads are orphaned. Check the local security note S-4 before fixing. #8's review deferred more lifecycle items here: `disconnect` kills threads without joining, `@shutdown` is shared rather than per-connection, and `connected?` ignores the heartbeat thread.
- **F-23 / [#10](https://github.com/MadBomber/smart_message-transport-lanet/issues/10):** blank `LANET_*` env vars become 0. Small helper plus a test.
- **F-20 / [#11](https://github.com/MadBomber/smart_message-transport-lanet/issues/11):** `logger` isn't declared in the gemspec. One gemspec line (never self-merged).
- **F-25 / [#12](https://github.com/MadBomber/smart_message-transport-lanet/issues/12):** a configure test leaks live threads. About 3 test lines.
- **F-15:** RuboCop baseline (`.rubocop_todo.yml`). The default `rake` task is red with 273 offenses. Regenerate after #13 merges.
- **F-21:** move to minitest 6 (`~> 6.0` plus `minitest-mock`). After #13.
- **F-7 / [#15](https://github.com/MadBomber/smart_message-transport-lanet/issues/15):** drop the `message.to_s` payload fallback in `handle_smart_message`. 1 lib line + a fail-before test (improver verified). Not in #9's hunk.
- **F-30 / [#14](https://github.com/MadBomber/smart_message-transport-lanet/issues/14):** the default logger is `SmartMessage::Logger.new` (a module) → `NoMethodError`. Default to `SmartMessage::Logger.default`; build with/after #3, check local security note first.
- **F-33 / [#16](https://github.com/MadBomber/smart_message-transport-lanet/issues/16):** gemspec metadata (bug tracker, docs, MFA URIs). Gemspec, never self-merged.
- **F-28 (half):** remove dead `setup`/`teardown` in `test_lanet_concurrency.rb:6-13` (−8 lines, test only, low-risk self-merge candidate).
- **Next visit when the queue drains:** #12 fix (test-only, ~2 lines, low-risk self-merge) is ranked first by triage and improver.
- **New (from #9):** `unsubscribe!` isn't overridden, so its handler stays in `@message_handlers` and keeps being advertised in heartbeats.

## Open bomberbot PRs

- [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8) `bomberbot/reconnect-after-disconnect`, "fix: allow connect after disconnect". Fixes #4 (F-6). Resets `@shutdown` in `configure`; 1 lib line + 2 tests + CHANGELOG. CI green; review hit the 3-round cap with no open defects. Touches `lib/`, so not low risk. (2026-11-12)

- [#9](https://github.com/MadBomber/smart_message-transport-lanet/pull/9) `bomberbot/class-subscribe-key`, "fix: key Lanet message handlers by class name". Fixes #2 (F-5). Handler keys are normalized to the class-name String; `network_topology[:active_subscriptions]` now returns Strings. lib +5/-7, 4 tests, CHANGELOG. CI green; review hit the 3-round cap (rounds 2 and 3 contradicted each other). Touches `lib/`, so not low risk. (2026-11-12)

- [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13) `bomberbot/relock-ruby4`, "build: relock dev dependencies so the bundle installs on Ruby 4". `Gemfile.lock` (7 conservative minor/patch bumps, platform `ruby` only so 3.3 installs), Ruby 4.0 added to the CI matrix, CHANGELOG. CI green on 3.3/3.4/4.0. Review hit the 3-round cap with no open defects. Touches the lockfile and `.github/`, so not low risk. (2026-11-12)

## Merged bomberbot PRs

- **merged by Dewayne** [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5) `bomberbot/drop-ostruct`, "test: load suite on Ruby 4 and minitest 6". Replaces the one `OpenStruct` with a `Struct` and requires `minitest/mock`. Touches test files and CHANGELOG only, 6 lines. Review clean after 2 rounds. Not self-merged, because round 1 deferred an out-of-scope note (an unused dispatcher stub), which counts as open under the sqa#46 rule. Source: finding F-3.
- **merged by Dewayne** [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6) `bomberbot/ci-tests`, "ci: run the test suite on Ruby 3.3 and 3.4". Live CI is green. Hit the 3-round review cap, and every round-3 finding was fixed. Not low risk, because it touches `.github/`. Source: finding F-12.
- **merged by Dewayne** [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7) `bomberbot/readme-api-accuracy`, "docs(readme): match examples to smart_message 0.0.17 API" (`property`, `Class.subscribe`, `message.to`, binding the transport). README only, 30 lines, MERGEABLE. Review clean after 2 rounds, but round 1 rejected 8 transport-code issues as out of scope (now F-18, F-19, S-7), so it was not self-merged. Source: improver, F-14.
