# smart_message

A Ruby gem (v0.0.18 on `develop`; 0.0.17 on RubyGems, 2025-09-12) that turns messages
into self-describing `Hashie::Dash` objects which validate, address (from/to/reply_to),
version, and route themselves over pluggable transports (memory, stdout, file, FIFO,
Redis; RabbitMQ and others live in separate `sm-transport-*` gems). It adds a dispatcher
with filters, deduplication, a circuit breaker, and a file-backed dead letter queue.
README calls it alpha.

## Links

- GitHub: https://github.com/MadBomber/smart_message
- RubyGems: https://rubygems.org/gems/smart_message
- Docs: https://madbomber.github.io/smart_message/
- Issues: https://github.com/MadBomber/smart_message/issues

## Status

_Updated 2026-11-10 (third visit)._ Review queue still full (#9, #10, #11 unreviewed, `develop`
unmoved at 9371456), so no new PRs. Ran the first **dependencies** audit plus a brief quality
pass and an improver read of the dispatcher, DDQ, and circuit breaker (F-38–F-59). Filed
[#15](https://github.com/MadBomber/smart_message/issues/15) (gem can't be eager-loaded: Zeitwerk
expects `Ddq`), [#16](https://github.com/MadBomber/smart_message/issues/16) (subclass dedup
silently off), [#17](https://github.com/MadBomber/smart_message/issues/17)
(`CircuitBreaker.stats`/`available?` always raise).

_Second visit (2026-11-10).__ Verification-only visit: Dewayne hasn't reviewed,
merged, or commented on any of the three open bomberbot PRs (#9, #10, #11) yet, so with
3 unapproved PRs open and none low-risk-mergeable the **review queue was full** — no new
PRs this visit. `develop` still at 9371456, unmoved. Ran the **adoption audit** (first
ever): docs promote a `:redis_queue` transport, a MessagePack serializer, and an
`examples/redis_queue/` suite that don't exist (F-24–F-26); `FileTransport` is documented
but unregistered (F-27). Filed issues [#12](https://github.com/MadBomber/smart_message/issues/12)
(FileTransport not registered → `create(:file)` nil), [#13](https://github.com/MadBomber/smart_message/issues/13)
(broken `<string>` tag on docs homepage), [#14](https://github.com/MadBomber/smart_message/issues/14)
(dead `debug_me` code in tests). Findings F-24–F-37.

_First visit (2026-11-10)._ `develop` (9371456) is 6 commits ahead of `main`;
0.0.18 (Ruby 4.1 support, `to_h` fix) is unreleased. Bomberbot opened three PRs against
`develop`: test CI ([#11](https://github.com/MadBomber/smart_message/pull/11), green on Ruby 4.0 and 4.1.0-preview1 with Redis),
a DLQ `inspect_messages` fix ([#10](https://github.com/MadBomber/smart_message/pull/10)) and a docs-site script removal ([#9](https://github.com/MadBomber/smart_message/pull/9)).
Filed issues #6 to #8. Suite green on 4.1.0-preview1: 316 runs, 940 assertions,
0F/0E/27 skips. The locked gems need Ruby 4.0, but the gemspec still says `>= 3.0.0`.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- F-11 `DeadLetterQueue#inspect_messages(limit:)` returns `[]` past the limit. **PR open** [#10](https://github.com/MadBomber/smart_message/pull/10).
- [#6](https://github.com/MadBomber/smart_message/issues/6) `replay_all` loses an entry whose replay fails (F-12, F-8): bound the loop to the starting size, plus a raw re-append helper. Top pick next visit (same file as #10; stack or wait).
- [#7](https://github.com/MadBomber/smart_message/issues/7) README quick start/examples fail (`Serializer::JSON`, class-level `serializer`, `publish` vs `do_publish`) (F-16).
- [#8](https://github.com/MadBomber/smart_message/issues/8) gem ships `.idea/`, a `.backup` transport, and a 1.7 MB DLQ log (F-7): `git rm` only.
- Gemspec floor `>= 3.0.0` is wrong: breaker_machines 0.21.2 / chrono_machines 0.11.0 in the lock need Ruby 4.0 (CI proved it on #11). Raise to `>= 4.0` or loosen those deps; Dewayne's call (F-10).
- F-13/F-23: the suite writes `dead_letters.jsonl`, `file*.log`, `messages.log`, `log/` into the repo root and shares `/tmp/test_dlq.jsonl`; a tests-only tmpdir PR (low risk).
- F-1 duplicate `TransportNotConfigured`; F-2 dead private `Logger::Default#message_summary`/`#truncate`: one small removal PR.
- F-17 `examples/README.md` index: all 18 paths wrong (docs only, low risk).
- F-3 `Serializer::Base#do_encode` should raise `NotImplemented`.
- F-20 `gem build smart_message.gemspec` fails with a relative path (`Dir.chdir(__dir__)`), F-18 gemspec URIs point at `/blob/master/`: one gemspec PR.
- MathJax loads on every docs page though no page uses math (builder note on #9).
- [#12](https://github.com/MadBomber/smart_message/issues/12) `FileTransport` is documented but never registered, so `Transport.create(:file)` returns nil (F-27): one-line `register(:file, FileTransport)` fix.
- [#13](https://github.com/MadBomber/smart_message/issues/13) docs homepage opens with a broken `<string>` tag so the lead sentence never renders bold (F-32): `docs/index.md:10`, docs only.
- [#14](https://github.com/MadBomber/smart_message/issues/14) dead `debug_me` code in the test suite — `Object#debug_me_context` monkeypatch + 3 stray `debug_me` calls (F-33/F-34): pure removal.
- F-30 duplicate `## [0.0.1]` CHANGELOG section (`CHANGELOG.md:651` and `:697`): renumber/merge one, docs only.
- [#15](https://github.com/MadBomber/smart_message/issues/15) gem can't be eager-loaded (F-38): one `loader.inflector.inflect("ddq" => "DDQ")` line plus an eager-load test. Top pick when the queue frees (Rails production boot breaks).
- [#16](https://github.com/MadBomber/smart_message/issues/16) `enable_deduplication!` on a subclass leaves dedup off (F-53): default fallbacks in `ddq_config`/builder plus a test.
- [#17](https://github.com/MadBomber/smart_message/issues/17) `CircuitBreaker.stats`/`available?` always raise (F-55): rewrite on the BreakerMachines API `transport_circuit_stats` uses.
- F-39 declare `logger` as a runtime dependency (bundled gem since Ruby 4.0); gemspec, one line.
- F-48 flaky wall-clock test `test_concurrent_proc_handler_execution`: test-only fix; rule out a pool cause first.
- F-42/F-43 drop unused `mutex_m`, swap `shoulda` for `shoulda-context`: one dev-deps PR.
- F-40 lock bump (all green): after #11 merges.
- F-54 Redis DDQ never evicts its lookup SET; F-56 Redis DDQ ignores `redis_url:`/`redis_db:`.

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/smart_message/pull/9) `bomberbot/drop-polyfill-script` → develop. docs: drop unused polyfill script. Review clean. Labeled `needs-dewayne` (live site changes only via `main`). Security fix, so not merged as low risk.
- [#10](https://github.com/MadBomber/smart_message/pull/10) `bomberbot/fix-dlq-inspect-limit` → develop. fix(dlq): `inspect_messages` returns the first `limit` entries. New tests fail on develop, pass on branch; suite 319/947/0F/27S. Review clean (2 rounds). Touches `lib/`, so not low risk.
- [#11](https://github.com/MadBomber/smart_message/pull/11) `bomberbot/test-ci` → develop. ci: test workflow (Ruby 4.0, 4.1.0-preview1, redis:7), deletes `.travis.yml`, adds x86_64-linux to the lock. CI green. Review hit the 3-round cap with no defects left. Ruby 3.3/3.4 dropped from the matrix (locked gems need 4.0).
- All three add a CHANGELOG `[Unreleased]` line; the second and third to merge need a trivial keep-both conflict fix.

## Merged bomberbot PRs

- (none)
