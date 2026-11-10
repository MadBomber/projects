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

_Updated 2026-11-10 (first visit)._ `develop` (9371456) is 6 commits ahead of `main`;
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

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/smart_message/pull/9) `bomberbot/drop-polyfill-script` → develop. docs: drop unused polyfill script. Review clean. Labeled `needs-dewayne` (live site changes only via `main`). Security fix, so not merged as low risk.
- [#10](https://github.com/MadBomber/smart_message/pull/10) `bomberbot/fix-dlq-inspect-limit` → develop. fix(dlq): `inspect_messages` returns the first `limit` entries. New tests fail on develop, pass on branch; suite 319/947/0F/27S. Review clean (2 rounds). Touches `lib/`, so not low risk.
- [#11](https://github.com/MadBomber/smart_message/pull/11) `bomberbot/test-ci` → develop. ci: test workflow (Ruby 4.0, 4.1.0-preview1, redis:7), deletes `.travis.yml`, adds x86_64-linux to the lock. CI green. Review hit the 3-round cap with no defects left. Ruby 3.3/3.4 dropped from the matrix (locked gems need 4.0).
- All three add a CHANGELOG `[Unreleased]` line; the second and third to merge need a trivial keep-both conflict fix.

## Merged bomberbot PRs

- (none)
