# smart_message

A Ruby gem (0.1.0 on `develop`, unreleased; 0.0.17 on RubyGems, 2025-09-12) that turns
messages into self-describing `Hashie::Dash` objects which validate, address
(from/to/reply_to), version, and route themselves over pluggable transports (memory,
stdout, append-only JSON Lines file, Redis; others live in separate `sm-transport-*`
gems). It adds a bounded dispatcher with per-handler circuit breakers and deduplication,
a `PublishResult` per publish, and a locked, file-backed dead letter queue. Requires
Ruby 4.0+. The repo now has its own `CLAUDE.md`; read it before building.

## Links

- GitHub: https://github.com/MadBomber/smart_message
- RubyGems: https://rubygems.org/gems/smart_message
- Docs: https://madbomber.github.io/smart_message/
- Issues: https://github.com/MadBomber/smart_message/issues

## Status

_Updated 2026-11-12 (fifth visit)._ Skipped new PRs: review queue full (#21, #22, #23 open,
green, unreviewed; tender changed nothing). Ran the full **dead code** audit at 07af3d4
(F-71..F-77) and filed [#24](https://github.com/MadBomber/smart_message/issues/24) (decode
errors skip the DLQ), [#25](https://github.com/MadBomber/smart_message/issues/25) (Pathname
logger) and [#26](https://github.com/MadBomber/smart_message/issues/26) (Symbol transport).

_Earlier, 2026-11-12 (fourth visit)._ Dewayne merged #9, #10 and #11, then pushed the 0.1.0
rewrite (07af3d4, "feat!: release 0.1.0 with reliable delivery and a smaller API") to
`develop`. It fixes almost the whole old backlog, including issues #6, #7, #8, #12, #13, #15,
#16 and #17; BomberBot commented on each with evidence, and Dewayne can close them. Suite
443 runs / 1750 assertions / 0F/0E/0S, `rake examples` 21/21, CI green on develop. Ran the
second **security** audit (S-8–S-12 held locally) and filed
[#18](https://github.com/MadBomber/smart_message/issues/18) (Redis subscriber gives up for good),
[#19](https://github.com/MadBomber/smart_message/issues/19) (no LICENSE) and
[#20](https://github.com/MadBomber/smart_message/issues/20) (unresolvable handlers accepted).
Opened #21, #22 and #23 (see below). `main` is still at the 2025-09-12 state, 11 commits
behind; 0.0.18 and 0.1.0 are untagged.

_Earlier visits (2026-11-10, three of them)._ First visit opened #9–#11 and filed #6–#8;
the second and third ran adoption and dependency audits with the review queue full and
filed #12–#17. All superseded by 0.1.0; see `bomberbot/findings/smart_message.md`.

## Backlog

Well-defined items a single small PR could close, re-checked at develop 07af3d4. Bomberbot
works these before anything else in this repo.

- [#14](https://github.com/MadBomber/smart_message/issues/14) stray `debug_me` calls and a routing test that can't fail (F-61). **PR open** [#21](https://github.com/MadBomber/smart_message/pull/21).
- F-62 DLQ reference page misdescribes replay. **PR open** [#22](https://github.com/MadBomber/smart_message/pull/22).
- DLQ replay should rebuild an entry as the class it was stored under. **PR open** [#23](https://github.com/MadBomber/smart_message/pull/23).
- No end-to-end test of a channelless transport (file) through the DLQ to replay; `statistics[:by_class]` buckets `A::Order` and `B::Order` together (`split("::").last`).
- `test/publish_test.rb:25-40` and `test/proc_handler_test.rb:33` processors only `puts`, so nothing checks the messages arrived (same pattern as #14); `subscribe_test` never shuts its dispatcher down. Test-only; after #21.
- [#24](https://github.com/MadBomber/smart_message/issues/24) wrap non-SmartMessage errors in `Envelope.decode` as `DecodeError` so `receive` dead-letters them (F-78); `lib/`, ~6 lines + tests.
- [#25](https://github.com/MadBomber/smart_message/issues/25) accept `Pathname` in `config.logger=`/Lumberjack, reject non-loggers (F-79).
- [#26](https://github.com/MadBomber/smart_message/issues/26) reject a non-transport value in the `transport` setter (F-80).
- F-71 + F-72 delete two dead test helpers and two unused test requires; test-only, low risk (after #23).
- F-73 + F-74 delete 77 lines of uncalled city_scenario dashboard builders and example leftovers; examples-only, low risk.
- F-77 tests for documented `replay_batch`, `configure_default`, `reset_logger`; test-only (after #23).
- [#18](https://github.com/MadBomber/smart_message/issues/18) Redis subscriber gives up after `reconnect_attempts` while `connected?` stays true (F-60).
- [#19](https://github.com/MadBomber/smart_message/issues/19) add LICENSE.txt (MIT) + `spec.license` (F-63); gemspec, so not low risk.
- [#20](https://github.com/MadBomber/smart_message/issues/20) validate String/Symbol handlers at `subscribe` (F-64).
- F-65 run `rake examples` in `.github/workflows/test.yml` (21/21 in ~10 s; push needs the `workflow` OAuth scope).
- F-66 `bundle update --conservative activesupport` (8.1.4 → 8.1.4.1), suite green; lockfile only.
- S-9, S-12: security findings, details held locally; each a small neutral-titled PR.
- F-30 duplicate `## [0.0.1]` CHANGELOG section (`CHANGELOG.md:712` and `:758`): merge the bodies.
- MathJax (`pymdownx.arithmatex`, `mkdocs.yml:94,131-132`) and the `macros` plugin are unused by any page; `mkdocs.yml`, so never low risk.
- F-39 declare `logger` as a runtime dependency; F-42/F-43 drop `mutex_m`, `shoulda` → `shoulda-context`: gemspec PRs.
- F-46 redundant `require 'set'` (`utilities.rb:5`, `transport/redis_transport.rb:7`); trivial.
- F-31 add `docs/llms.txt` (prompt_manager#24 precedent).

## Open bomberbot PRs

- [#21](https://github.com/MadBomber/smart_message/pull/21) `bomberbot/assert-dispatcher-routing` → develop. test(dispatcher): assert routed messages reach both processors. Fixes #14. Test-only (+30/−87). New test fails under a no-op `route` and a no-op delivery probe; develop's passes under both. Suite 443/1760/0F/0E/0S; CI green. Review hit the 3-round cap (round 3 caught and reverted a stray CHANGELOG heading rename); no open defects. Cap hit, so not self-merged.
- [#22](https://github.com/MadBomber/smart_message/pull/22) `bomberbot/dlq-replay-docs` → develop. docs: match DLQ replay notes to the code. 2 lines in `docs/reference/dead-letter-queue.md`. Repro scripts; suite 443/0F; `mkdocs build --strict` clean (macros removed in a scratch copy). CI green. Review hit the 3-round cap with nothing open against the diff. Cap hit, so not self-merged.
- [#23](https://github.com/MadBomber/smart_message/pull/23) `bomberbot/replay-stored-class` → develop. fix(dlq): replay a dead letter only as the class it was stored for. `lib/` (dead_letter_queue.rb, one line in transport/base.rb), tests, one docs sentence, CHANGELOG line. New tests fail on develop (2F+1E), pass on branch; suite 447/1776/0F/0E/0S; examples 21/21; CI green. Review hit the 3-round cap, no defects open. Touches `lib/`, so not low risk. #21 and #23 each add a CHANGELOG `[Unreleased]` line, so the second to merge needs a keep-both fix.

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/smart_message/pull/9) docs: drop unused polyfill script (merge commit, 2026-11-10). Live site still serves `main`'s old `mkdocs.yml` until a release.
- [#10](https://github.com/MadBomber/smart_message/pull/10) fix(dlq): `inspect_messages` returns the first `limit` entries (squash, 2026-11-10). Kept verbatim through the 0.1.0 rewrite.
- [#11](https://github.com/MadBomber/smart_message/pull/11) ci: test workflow on Ruby 4.0/4.1.0-preview1 with Redis (squash, 2026-11-11; Dewayne asked Copilot to resolve its CHANGELOG conflict).
