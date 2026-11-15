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

_Updated 2026-11-15 (eleventh visit)._ Dewayne squash-merged everything in flight (#36–#50, bomberbot #54–#56, his #60–#65 fixing #51–#59; `develop` 2bf06e8, CI green, 0 open issues). Opened [#69](https://github.com/MadBomber/smart_message/pull/69) (F-160), [#70](https://github.com/MadBomber/smart_message/pull/70) (F-163) and [#71](https://github.com/MadBomber/smart_message/pull/71) (F-165), all `lib/`, so none self-merged; they merge cleanly together (combined suite 489/0F). Ran an incremental **quality** audit (F-164..F-174; improver F-180..F-182) and filed [#66](https://github.com/MadBomber/smart_message/issues/66) (dispatcher settings), [#67](https://github.com/MadBomber/smart_message/issues/67) (FileTransport double log) and [#68](https://github.com/MadBomber/smart_message/issues/68) (`check_named_handler!`).

_Earlier, 2026-11-13 (tenth visit)._ Skipped new PRs: review queue full (#54, #55, #56 open, green, unreviewed; `develop` still 2aa3dbf; Dewayne's #36–#50 unchanged). Ran an incremental **dead code** audit (F-155, F-156) and filed [#57](https://github.com/MadBomber/smart_message/issues/57) (Module/Set `validate:`), [#58](https://github.com/MadBomber/smart_message/issues/58) (one-argument `required:` lambda) and [#59](https://github.com/MadBomber/smart_message/issues/59) (`unsubscribe` with a lambda).

_Earlier, 2026-11-13 (ninth visit)._ Dewayne merged #21, #22, #23 (`develop` 2aa3dbf) and opened his own PRs [#36](https://github.com/MadBomber/smart_message/pull/36)–[#50](https://github.com/MadBomber/smart_message/pull/50), one per open issue (#18–#35), all green; BomberBot checked each against its issue's repro (all fix it; #46 covers 3 of #20's 4 cases, commented). Opened [#54](https://github.com/MadBomber/smart_message/pull/54) (drop unused example code), [#55](https://github.com/MadBomber/smart_message/pull/55) (tests for `replay_batch`/`configure_default`/`reset_logger`) and [#56](https://github.com/MadBomber/smart_message/pull/56) (wait for dispatch instead of sleeps); none self-merged. Ran the **security** audit (F-137, F-138, S-14) and filed [#51](https://github.com/MadBomber/smart_message/issues/51), [#52](https://github.com/MadBomber/smart_message/issues/52), [#53](https://github.com/MadBomber/smart_message/issues/53).

_Earlier, 2026-11-12 (eighth visit)._ Skipped new PRs a fourth time (review queue full: #21, #22, #23 open, green, unreviewed; `develop` still 07af3d4). Ran the **dependencies** audit (F-120..F-123: dependencies healthy on 4.0 and 4.1, but keep `hashie ~> 5.0`; see F-120) and filed [#33](https://github.com/MadBomber/smart_message/issues/33) (`clear_ddq!` hits nested classes), [#34](https://github.com/MadBomber/smart_message/issues/34) (transport options that hang) and [#35](https://github.com/MadBomber/smart_message/issues/35) (`pretty_print` with Integer keys).

_Earlier, 2026-11-12 (seventh visit)._ Skipped new PRs a third time (review queue full: #21, #22, #23 open, green, unreviewed; `develop` still 07af3d4). Ran the **adoption** audit (F-94..F-98; README, examples, `mkdocs --strict` all clean) and filed [#30](https://github.com/MadBomber/smart_message/issues/30) (unchecked `log_level`/`log_format`), [#31](https://github.com/MadBomber/smart_message/issues/31) (setup errors don't name the class or fix) and [#32](https://github.com/MadBomber/smart_message/issues/32) (gemspec URIs).

_Earlier, 2026-11-12 (sixth visit)._ Skipped new PRs again (review queue full: #21, #22 and #23 are still open, green and unreviewed; `develop` is unchanged at 07af3d4). Ran the **quality** audit (F-82..F-85; the improver added F-90..F-93) and filed [#27](https://github.com/MadBomber/smart_message/issues/27) (partial subscribe not rolled back), [#28](https://github.com/MadBomber/smart_message/issues/28) (configuration docs set dispatcher limits too late) and [#29](https://github.com/MadBomber/smart_message/issues/29) (nil `dlq_path`).

_Earlier, 2026-11-12 (fifth visit)._ Skipped new PRs: review queue full (#21, #22, #23 open,
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

- [#14](https://github.com/MadBomber/smart_message/issues/14) stray `debug_me` calls and a routing test that can't fail (F-61). Merged in [#21](https://github.com/MadBomber/smart_message/pull/21) (2026-11-12).
- F-62 DLQ reference page misdescribes replay. Merged in [#22](https://github.com/MadBomber/smart_message/pull/22) (2026-11-12).
- DLQ replay should rebuild an entry as the class it was stored under. Merged in [#23](https://github.com/MadBomber/smart_message/pull/23) (2026-11-12).
- No end-to-end test of a channelless transport (file) through the DLQ to replay; `statistics[:by_class]` buckets `A::Order` and `B::Order` together (`split("::").last`).
- `test/publish_test.rb:25-40` and `test/proc_handler_test.rb:33` processors only `puts`, so nothing checks the messages arrived (same pattern as #14); `subscribe_test` never shuts its dispatcher down. Test-only; after #21.
- [#24](https://github.com/MadBomber/smart_message/issues/24) wrap non-SmartMessage errors in `Envelope.decode` as `DecodeError` so `receive` dead-letters them (F-78); `lib/`, ~6 lines + tests. Merged in [#47](https://github.com/MadBomber/smart_message/pull/47) (2026-11-13).
- [#25](https://github.com/MadBomber/smart_message/issues/25) accept `Pathname` in `config.logger=`/Lumberjack, reject non-loggers (F-79). Merged in [#43](https://github.com/MadBomber/smart_message/pull/43) (2026-11-13).
- [#26](https://github.com/MadBomber/smart_message/issues/26) reject a non-transport value in the `transport` setter (F-80). Merged in [#44](https://github.com/MadBomber/smart_message/pull/44) (2026-11-13).
- F-71 + F-72 delete two dead test helpers and two unused test requires; test-only, low risk (after #23). DLQ half of F-71 Merged in [#55](https://github.com/MadBomber/smart_message/pull/55) (2026-11-13); `reset_counters` half (multi_transport_test, touched by #44/#45/#48) and F-72 still open.
- F-73 + F-74 delete 77 lines of uncalled city_scenario dashboard builders and example leftovers; examples-only, low risk. Merged in [#54](https://github.com/MadBomber/smart_message/pull/54) (2026-11-13).
- F-77 tests for documented `replay_batch`, `configure_default`, `reset_logger`; test-only (after #23). Merged in [#55](https://github.com/MadBomber/smart_message/pull/55) (2026-11-13).
- [#18](https://github.com/MadBomber/smart_message/issues/18) Redis subscriber gives up after `reconnect_attempts` while `connected?` stays true (F-60). Merged in [#50](https://github.com/MadBomber/smart_message/pull/50) (2026-11-13).
- [#19](https://github.com/MadBomber/smart_message/issues/19) add LICENSE.txt (MIT) + `spec.license` (F-63); gemspec, so not low risk. Merged in [#39](https://github.com/MadBomber/smart_message/pull/39) (2026-11-13).
- [#20](https://github.com/MadBomber/smart_message/issues/20) validate String/Symbol handlers at `subscribe` (F-64). Merged in [#46](https://github.com/MadBomber/smart_message/pull/46) (2026-11-13).
- F-65 run `rake examples` in `.github/workflows/test.yml` (21/21 in ~10 s; push needs the `workflow` OAuth scope).
- F-66 `bundle update --conservative activesupport` (8.1.4 → 8.1.4.1), suite green; lockfile only.
- S-9, S-12: security findings, details held locally; each a small neutral-titled PR.
- F-30 duplicate `## [0.0.1]` CHANGELOG section (`CHANGELOG.md:712` and `:758`): merge the bodies.
- MathJax (`pymdownx.arithmatex`, `mkdocs.yml:94,131-132`) and the `macros` plugin are unused by any page; `mkdocs.yml`, so never low risk.
- F-39 declare `logger` as a runtime dependency; F-42/F-43 drop `mutex_m`, `shoulda` → `shoulda-context`: gemspec PRs.
- F-46 redundant `require 'set'` (`utilities.rb:5`, `transport/redis_transport.rb:7`); trivial.
- F-31 add `docs/llms.txt` (prompt_manager#24 precedent).
- [#28](https://github.com/MadBomber/smart_message/issues/28) reorder the dispatcher settings in `docs/core-concepts/configuration.md` and example 14, and add an `Example.check` (F-90); docs and examples only, so low risk. Merged in [#49](https://github.com/MadBomber/smart_message/pull/49) (2026-11-13).
- [#29](https://github.com/MadBomber/smart_message/issues/29) fall back to the default DLQ path for a nil or blank `dlq_path` (F-92); `lib/`, about 2 lines plus a test. Merged in [#41](https://github.com/MadBomber/smart_message/pull/41) (2026-11-13).
- [#27](https://github.com/MadBomber/smart_message/issues/27) roll back earlier transports when `subscribe` fails partway (F-82); `lib/` + test. Merged in [#45](https://github.com/MadBomber/smart_message/pull/45) (2026-11-13).
- F-83 replace 24 fixed sleeps in three test files with `wait_for_dispatch` (test-only, low risk; pairs with F-48). Merged in [#56](https://github.com/MadBomber/smart_message/pull/56) (2026-11-13) (message_filtering + proc_handler_integration); `proc_handler_test.rb` (7 sleeps) waits for #46.
- F-91 add the city_scenario smoke test to `rake examples` with a Redis skip (examples only, low risk).
- F-98 + F-30 normalise CHANGELOG headings and merge the duplicate `[0.0.1]`; CHANGELOG-only, low risk once #21/#23 have merged (they touch CHANGELOG).
- [#30](https://github.com/MadBomber/smart_message/issues/30) validate `log_level`/`log_format` at assignment (F-110); `lib/configuration.rb` (same file as #29/#25), ~20 lines + test. Merged in [#42](https://github.com/MadBomber/smart_message/pull/42) (2026-11-13).
- [#31](https://github.com/MadBomber/smart_message/issues/31) name the class and the fix in the missing-`from` and `TransportNotConfigured` errors (F-113/F-114); a few strings + test + troubleshooting doc. Merged in [#48](https://github.com/MadBomber/smart_message/pull/48) (2026-11-13).
- [#32](https://github.com/MadBomber/smart_message/issues/32) gemspec `homepage`/`documentation_uri` → docs site (F-94); gemspec, pair with #19. Merged in [#40](https://github.com/MadBomber/smart_message/pull/40) (2026-11-13).
- F-111 + S-13 short `inspect` for transports and `PublishResult` (neutral title); `lib/`, ~25 lines + test.
- [#33](https://github.com/MadBomber/smart_message/issues/33) build `ddqs_of` from the class's own subscriptions, not a string prefix (F-131); `lib/dispatcher.rb`, ~10 lines + test. Merged in [#38](https://github.com/MadBomber/smart_message/pull/38) (2026-11-13).
- [#34](https://github.com/MadBomber/smart_message/issues/34) reject `max_messages` ≤ 0 and non-positive `poll_interval` in transport `configure` (F-130/F-133); `lib/`, ~20 lines + tests. Merged in [#37](https://github.com/MadBomber/smart_message/pull/37) (2026-11-13).
- [#35](https://github.com/MadBomber/smart_message/issues/35) `deep_symbolize_keys` keeps keys that don't respond to `to_sym` (F-132); one line + test. Merged in [#36](https://github.com/MadBomber/smart_message/pull/36) (2026-11-13).
- F-122 (+F-39, F-123) one gemspec dev-dependency batch: drop `mutex_m`/`minitest-power_assert`/`minitest-mock`, `shoulda` → `shoulda-context`, declare `logger`, `zeitwerk ~> 2.7`; verified green; gemspec, never low risk.
- [#51](https://github.com/MadBomber/smart_message/issues/51) pass the transport's serializer to `enqueue_raw` so raw dead letters from a non-JSON serializer can be replayed (F-137); `transport/base.rb` one line + test. Fixed by [#64](https://github.com/MadBomber/smart_message/pull/64) (2026-11-13).
- [#52](https://github.com/MadBomber/smart_message/issues/52) logger contract tests that can't fail (F-152); test-only, after #43. Fixed by [#65](https://github.com/MadBomber/smart_message/pull/65) (2026-11-13).
- [#53](https://github.com/MadBomber/smart_message/issues/53) honour `ddq_storage :redis, key_prefix:` (F-151); `dispatcher.rb`, after #38. Fixed by [#63](https://github.com/MadBomber/smart_message/pull/63) (2026-11-13).
- F-138 DLQ docs: replay of a payload with no `_sm_header` fails with `ValidationError`, not a decode error; one docs sentence.
- S-14: security finding, details held locally; small neutral-titled `lib/` PR.
- [#57](https://github.com/MadBomber/smart_message/issues/57) `validate:` with a Module or Set (F-157): `when Module` / `when Array, Set` in `property_validations.rb`, + tests + docs row; ~2 lines. Fixed by [#60](https://github.com/MadBomber/smart_message/pull/60) (2026-11-13).
- [#58](https://github.com/MadBomber/smart_message/issues/58) pass the message to a one-argument `required:` lambda (F-158); `property_validations.rb:127`, ~3 lines + test. Fixed by [#61](https://github.com/MadBomber/smart_message/pull/61) (2026-11-13).
- [#59](https://github.com/MadBomber/smart_message/issues/59) `unsubscribe` rejects a callable with a hint to pass the id (F-159); `subscription.rb`, ~4 lines + test. Fixed by [#62](https://github.com/MadBomber/smart_message/pull/62) (2026-11-13).
- F-160 `subscribe("X.y") { }` ignores the named handler: raise `ArgumentError`; after #46 (same file). **PR open** [#69](https://github.com/MadBomber/smart_message/pull/69).
- F-163 `rescue NameError, TypeError` in `Envelope.constant_named`; after #47. **PR open** [#70](https://github.com/MadBomber/smart_message/pull/70). Same `rescue NameError`-only pattern remains in `subscription.rb:176-178` (issue [#68](https://github.com/MadBomber/smart_message/issues/68)), `dispatcher.rb:217` and `transport/registry.rb:40`.
- F-165 `required:` with a callable object or an optional-argument proc is always required (follows #61). **PR open** [#71](https://github.com/MadBomber/smart_message/pull/71). The `validate:` half waits on F-162's decision.
- [#66](https://github.com/MadBomber/smart_message/issues/66) check `dispatcher_max_threads`/`dispatcher_max_queue`/`shutdown_timeout` at assignment (F-180); `configuration.rb`, ~20 lines + tests + a docs sentence.
- [#67](https://github.com/MadBomber/smart_message/issues/67) FileTransport logs an undecodable line twice at ERROR (F-164); `file_transport.rb` ~3 lines + log test.
- [#68](https://github.com/MadBomber/smart_message/issues/68) make `check_named_handler!` private, reject non-constant targets, rescue `TypeError` (F-168); `subscription.rb`, after #69.
- F-143 example 14 logs to a closed logger: one line, examples-only, low risk. F-181 troubleshooting line for `subscriber_alive?`: docs-only, low risk. F-171 `log_level = :WARN` reads back `:WARN`: one line + test.

## Open bomberbot PRs

- [#69](https://github.com/MadBomber/smart_message/pull/69) `bomberbot/reject-handler-with-block` → develop. fix(subscribe): reject a handler passed together with a block (F-160). `subscription.rb` helper + test + docs line + CHANGELOG (top of Fixed). Red→green shown; suite 484/0F, examples 21/21; CI green; review clean in round 2. `lib/`, so not self-merged. One commit.
- [#70](https://github.com/MadBomber/smart_message/pull/70) `bomberbot/constant-named-typeerror` → develop. fix(envelope): report a header naming a non-module constant as an unknown class (F-163). One-line `rescue NameError, TypeError` + 2 tests + CHANGELOG (bottom of Fixed); suite 485/0F, examples 21/21; CI green; review hit the 3-round cap, nothing open. `lib/`, not self-merged. One commit.
- [#71](https://github.com/MadBomber/smart_message/pull/71) `bomberbot/required-callable` → develop. fix(validation): call a required: callable object or optional-argument proc with the message (F-165, follows #61). `property_validations.rb` + 7 tests + docs + CHANGELOG (after the #58 line); suite 486/0F, examples 21/21; CI green; review hit the 3-round cap, nothing open (round 1 caught a zero-arg `Method` regression). `lib/`, not self-merged. One commit.
- All three merge cleanly together in any order; combined suite 489/2063/0F.

## Merged bomberbot PRs

- [#54](https://github.com/MadBomber/smart_message/pull/54) refactor(examples): drop unused example code and a stray diagram (squash, 2026-11-13).
- [#55](https://github.com/MadBomber/smart_message/pull/55) test: cover replay_batch, configure_default and reset_logger (squash, 2026-11-13).
- [#56](https://github.com/MadBomber/smart_message/pull/56) test: wait for dispatch instead of fixed sleeps (squash, 2026-11-13).
- [#21](https://github.com/MadBomber/smart_message/pull/21) test(dispatcher): assert routed messages reach both processors (2026-11-12). Fixed #14.
- [#22](https://github.com/MadBomber/smart_message/pull/22) docs: match DLQ replay notes to the code (2026-11-12).
- [#23](https://github.com/MadBomber/smart_message/pull/23) fix(dlq): replay a dead letter only as the class it was stored for (2026-11-12; `develop` 2aa3dbf).
- [#9](https://github.com/MadBomber/smart_message/pull/9) docs: drop unused polyfill script (merge commit, 2026-11-10). Live site still serves `main`'s old `mkdocs.yml` until a release.
- [#10](https://github.com/MadBomber/smart_message/pull/10) fix(dlq): `inspect_messages` returns the first `limit` entries (squash, 2026-11-10). Kept verbatim through the 0.1.0 rewrite.
- [#11](https://github.com/MadBomber/smart_message/pull/11) ci: test workflow on Ruby 4.0/4.1.0-preview1 with Redis (squash, 2026-11-11; Dewayne asked Copilot to resolve its CHANGELOG conflict).
