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

_Updated 2026-11-10 (visit 1, resumed once): BomberBot created `develop` from `main` at c33acdf.
The main finding is that the transport calls a Lanet API that doesn't exist (F-1), so it can't
run against the real gem. How to fix that is Dewayne's call (inbox.md). This visit opened
plumbing and docs PRs [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5)
(tests on Ruby 4) and [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6)
(CI, green), and README API accuracy [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7). None were self-merged. It also filed issues #2–#4 for lifecycle bugs._

## Backlog

Well-defined items a single small PR could close.

- **F-1:** rebuild the transport on lanet's real API (recommended: `Lanet::Mesh`). **Needs Dewayne's decision** (inbox.md). Blocks most of the rest.
- **F-3:** make the test suite load on Ruby 4 and minitest 6. **PR open [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5).**
- **F-12:** add test CI. **PR open [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6)** (3.3 and 3.4; the Ruby floor question is in dewayne.md).
- **F-14:** make the README examples match the smart_message 0.0.17 API. **PR open [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7).**
- **F-5 / [#2](https://github.com/MadBomber/smart_message-transport-lanet/issues/2):** a subscription made with a Class never receives messages. About 4 lib lines plus a test.
- **F-2 / [#3](https://github.com/MadBomber/smart_message-transport-lanet/issues/3):** constructor ordering against the real `Base`; threads are orphaned. Check the local security note S-4 before fixing.
- **F-6 / [#4](https://github.com/MadBomber/smart_message-transport-lanet/issues/4):** the transport can't reconnect after `disconnect`.
- **F-15:** RuboCop baseline (`.rubocop_todo.yml`). The default `rake` task is red with 273 offenses.
- **F-16:** relock `Gemfile.lock` so the dev bundle installs on Ruby 4. This also clears the dev-lock advisories (S-6).
- **F-7:** drop the `message.to_s` payload fallback in `handle_smart_message`.

## Open bomberbot PRs

- [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8) `bomberbot/reconnect-after-disconnect`, "fix: allow connect after disconnect". Fixes #4 (F-6). Resets `@shutdown` in `configure`; 1 lib line + 2 tests + CHANGELOG. CI green; review hit the 3-round cap with no open defects. Touches `lib/`, so not low risk. (2026-11-12)

- [#9](https://github.com/MadBomber/smart_message-transport-lanet/pull/9) `bomberbot/class-subscribe-key`, "fix: key Lanet message handlers by class name". Fixes #2 (F-5). Handler keys are normalized to the class-name String; `network_topology[:active_subscriptions]` now returns Strings. lib +5/-7, 4 tests, CHANGELOG. CI green; review hit the 3-round cap (rounds 2 and 3 contradicted each other). Touches `lib/`, so not low risk. (2026-11-12)

## Merged bomberbot PRs

- **merged by Dewayne** [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5) `bomberbot/drop-ostruct`, "test: load suite on Ruby 4 and minitest 6". Replaces the one `OpenStruct` with a `Struct` and requires `minitest/mock`. Touches test files and CHANGELOG only, 6 lines. Review clean after 2 rounds. Not self-merged, because round 1 deferred an out-of-scope note (an unused dispatcher stub), which counts as open under the sqa#46 rule. Source: finding F-3.
- **merged by Dewayne** [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6) `bomberbot/ci-tests`, "ci: run the test suite on Ruby 3.3 and 3.4". Live CI is green. Hit the 3-round review cap, and every round-3 finding was fixed. Not low risk, because it touches `.github/`. Source: finding F-12.
- **merged by Dewayne** [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7) `bomberbot/readme-api-accuracy`, "docs(readme): match examples to smart_message 0.0.17 API" (`property`, `Class.subscribe`, `message.to`, binding the transport). README only, 30 lines, MERGEABLE. Review clean after 2 rounds, but round 1 rejected 8 transport-code issues as out of scope (now F-18, F-19, S-7), so it was not self-merged. Source: improver, F-14.
