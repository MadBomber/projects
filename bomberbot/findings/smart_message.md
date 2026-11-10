# Findings: smart_message

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-10 | First run, at develop 9371456. bundler-audit flags 2 activesupport advisories in the dev lockfile. No shell-outs, string eval, YAML/Marshal or ENV reads in `lib/`. S-1..S-6 held locally. |
| dead code | 2026-11-10 (lib only, brief) | debride over `lib/` at develop 9371456. True dead: F-1, F-2. Public-API orphans need a decision (F-6). Dependent `sm-transport-*` gems checked. |
| dependencies | n/a | Not yet run. Noted: unused runtime deps `async`/`async-redis` (F-5), floor `>= 3.0.0` (F-10), activesupport lock (S-5). `bundle outdated`: breaker_machines 0.21.2→0.24.1, chrono_machines 0.11.0→0.12.1, hashie 5.1.0→5.2.0, minitest 6.0.6→6.1.0, redis-client 0.30.1→0.31.0. |
| adoption | 2026-11-10 (2nd visit) | First run, at develop 9371456. `mkdocs build --strict` clean; memory examples run. `:redis_queue` transport + `examples/redis_queue/` + `docs/transports/redis-queue.md` all documented but nonexistent (F-24/25/26, needs decision); FileTransport unregistered so `create(:file)`→nil (F-27, issue #12); README `Serializer::JSON` casing NameError (F-28); CHANGELOG/tag drift + dup `[0.0.1]` (F-29/30); no llms.txt (F-31). Broken `<string>` docs homepage tag (F-32, issue #13); dead test debug code (F-33/34, issue #14). Obvious-yes: F-27, F-30, F-32, F-33/34. |
| quality | n/a | Not yet run. No `.rubocop.yml`. |

Dead-code scan last covered commit: 9371456 (develop, 2026-11-10; `lib/` only, brief)

## Findings

### 2026-11-10 (first visit; security audit + brief dead-code pass, develop 9371456)

- S-1: security finding, details held locally. Status: open (medium-high; needs Dewayne's decision on the fix shape).
- S-2: security finding, details held locally. Status: open (medium).
- S-3: security finding, details held locally. Status: open (low).
- S-4: security finding, details held locally. Status: open (low; small fix).
- S-5: security finding, details held locally. Status: open (low; dev lockfile only).
- S-6: security finding, details held locally. Status: open (informational).
- F-1: `Errors::TransportNotConfigured` is declared twice (`lib/smart_message/errors.rb:9` and `:13`). Fix: delete lines 11-13. Status: open, verified.
- F-2: `Logger::Default#message_summary` and `#truncate` (`lib/smart_message/logger/default.rb:147-165`, private) have no callers in lib/test/examples/docs or the `sm-transport-*` gems. Fix: delete both. Status: open, verified.
- F-3: default `Serializer::Base#do_encode` (`serializer/base.rb:60-66`) reads `message_hash[:_sm_payload]`, a key the flat format no longer has, so a subclass that doesn't override it silently encodes `"null"`. Fix: raise `NotImplemented` like `do_decode`. Status: open, verified.
- F-4: `RedisTransport#default_serializer` (`transport/redis_transport.rb:27-36`) tries to require a MessagePack serializer that doesn't exist, then falls back to JSON. Status: needs Dewayne's decision (build or drop MessagePack).
- F-5: runtime deps `async` and `async-redis` (`smart_message.gemspec:47-48`) are never required by `lib/`. Status: needs Dewayne's decision (same choice as F-4: the Redis Queue transport they were for doesn't exist).
- F-6: public API with no callers: `::MilClass` (`lib/smart_message.rb:7-8`, FIXME), `Errors::SerializerNotConfigured`, `CircuitBreaker.configure_entity_circuit`, `Fallbacks.retry_with_backoff`/`.graceful_degradation` (tests only). Status: needs Dewayne's decision (deprecation cycle).
- F-7: the gem ships developer files (`spec.files` is `git ls-files` minus tests): `.envrc`, `.irbrc`, `lib/smart_message/.idea/` (6 files), `transport/stdout_transport.rb.backup`, `examples/performance_metrics/dead_letters.jsonl`. Fix: `git rm` the `.idea` dir and `.backup`; an allowlist is a gemspec change. Status: issue #8.
- F-8: `DeadLetterQueue#enqueue(message, error_info = {})` is called with three arguments at `transport/base.rb:271`, `transport/async_publish_queue.rb:191`, `dead_letter_queue.rb:144`, `circuit_breaker.rb:176`, so a circuit trip, async retry exhaustion, or failed replay raises `ArgumentError` instead of recording the message. Status: open, verified; replay path tracked in issue #6.
- F-9: prior-analysis items still true at 9371456: `rescue => e; e.is_a?(Hash)` branches can never run (`serializer/base.rb:37,50`, `transport/base.rb:70`); circuit DSL redeclared in every `initialize`; one `at_exit` per Dispatcher; `@@transport`/`@@logger` shared across all message classes (`plugins.rb:14-15`). Status: open, verified by reading.
- F-10: `required_ruby_version '>= 3.0.0'` admits EOL Rubies (taste: drop them). Gemspec change. Status: open, verified.
- F-11: `DeadLetterQueue#inspect_messages(limit:)` returns `[]` whenever the queue holds more than `limit` entries (block-level `return` exits the method). Status: PR'd [#10](https://github.com/MadBomber/smart_message/pull/10).
- F-12: `DeadLetterQueue#replay_all` loses an entry whose replay fails: it is dequeued, then re-enqueued with three arguments, which raises (part of F-8). Status: issue #6.
- F-13: the test suite writes `dead_letters.jsonl`, `file1.log`, `file2.log`, `messages.log` and `log/` into the repo root. Status: open, verified (triage).
- F-14: version strings disagree: `mkdocs.yml:138` says 0.1.11, `docs/index.md:64` says v0.0.8, the gem is 0.0.18. Status: open, verified (triage).
- F-15: failed publishes never reach the DLQ and `publish` reports success, though the README says messages go to the DLQ when the circuit trips (`README.md:46-47`). Status: needs Dewayne's decision (should `publish` raise after dead-lettering?); depends on F-8/F-9.
- S-7: security finding, details held locally. Status: PR'd [#9](https://github.com/MadBomber/smart_message/pull/9).
- F-16: README code examples don't run: `Serializer::JSON` (real: `Json`) and a class-level `serializer` call that no longer exists (`README.md:148,573,743,1010`); the custom-transport example overrides `publish(header, payload)` instead of `do_publish(message_class, serialized_message)` (`README.md:773`). Status: issue #7.
- F-17: `examples/README.md` file index: all 18 referenced example paths are wrong. Status: open, verified (improver).
- F-18: gemspec `changelog_uri`/`documentation_uri` point at `/blob/master/`, which redirects to a branch named `old_stuff_to_delete`. Gemspec change. Status: open, verified (improver).
- F-19: `Transport.create(:unknown)` returns nil; `transport nil` is then the getter, so the class silently keeps its old transport (`transport.rb:30-33`, `plugins.rb:79-83`). Public return-value change. Status: open, verified (improver).
- F-20: `gem build smart_message.gemspec` (relative path) fails: `Dir.chdir(File.expand_path(__dir__, __FILE__))` at `smart_message.gemspec:32`; fix `Dir.chdir(__dir__)`. Status: open, verified (improver).
- F-21: `replay_one`/`replay_batch` drop a message whose replay fails (docs don't promise a re-queue). Status: needs Dewayne's decision.
- F-22: `DeadLetterQueue#read_entries_with_filter` reads the file without `@mutex` while `dequeue`/`clear` rewrite it in place, so concurrent `inspect_messages`/`statistics`/`filter_*` can see a half-written file; its `rescue JSON::ParserError` also wraps the caller's block. Status: unverified (builder note).
- F-23: `test/dead_letter_queue_test.rb` uses the fixed path `/tmp/test_dlq.jsonl`, so parallel suites collide (pairs with F-13). Status: open, verified by reading (builder note).

### 2026-11-10 (second visit; adoption audit + improver/triage, develop 9371456)

- F-24: README and docs promote a "Redis Queue Transport" (`:redis_queue`) that does not exist. `README.md:31,40,142-146,600-670` ("🌟 Redis Queue Transport (Featured)", `Transport.create(:redis_queue, ...)`, `transport.subscribe_pattern`, the `.where.from.to.subscribe` fluent API, "10x faster than RabbitMQ"); also `docs/reference/transports.md:20`, `docs/getting-started/quick-start.md:143`, `docs/getting-started/examples.md:827-835`, `docs/transports/multi-transport.md`. `Transport.create(:redis_queue)` → `nil`; registry registers only `:stdout/:memory/:redis` (`transport/registry.rb:54-56`); no `RedisQueueTransport`/`subscribe_pattern`/fluent `where` in `lib/`. Status: needs Dewayne's decision (implement vs remove the marketing). In inbox.
- F-25: README links to three nonexistent targets: `docs/transports/redis-queue.md`, `docs/guides/redis-queue-getting-started.md`, `examples/redis_queue/` (`README.md:31,670`). None exist. Status: open, verified (tied to F-24).
- F-26: `docs/getting-started/examples.md:827-835,853,879` documents an `examples/redis_queue/` suite (8 scripts) that does not exist. Status: open, verified (tied to F-24).
- F-27: `FileTransport` is shipped and documented (`lib/smart_message/transport/file_transport.rb`, `docs/transports/file-transport.md`) but never registered, so `Transport.create(:file)` → `nil` (`transport/registry.rb:52-56`). Examples instantiate `FileTransport.new` directly. Fix: `register(:file, FileTransport)`. Status: issue #12.
- F-28: README quick-start uses wrong serializer casing `SmartMessage::Serializer::JSON` (raises `NameError`; real class is `Json`) at `README.md:149,170`. Overlaps F-16/issue #7 (README API mismatch); fold into #7's fix. Status: verified, covered by issue #7.
- F-29: CHANGELOG headers and git tags are out of sync: `[0.0.14]`/`[0.0.15]` have no tags; tags `v0.0.3`/`v0.0.4`/`v0.0.12` have no CHANGELOG entries; `[0.0.18]` untagged. Status: open, verified (maintainer/release task, not a `src/` PR).
- F-30: duplicate `## [0.0.1] - 2025-08-17` CHANGELOG section with different bodies (`CHANGELOG.md:651` and `:697`). Fix: renumber/merge one. Status: open, verified (obvious-yes docs PR; not shipped this visit, queue full).
- F-31: no `llms.txt` published for the gem/docs site despite a live mkdocs site. Status: needs Dewayne's decision (nice-to-have). In inbox.
- F-32: docs homepage opens with a broken `<string>SmartMessage</strong>` tag (`docs/index.md:10`), so the lead sentence never renders bold. Fix: `<string>`→`<strong>`. Status: issue #13.
- F-33: dead global monkeypatch `Object#debug_me_context` (`test/test_helper.rb:3-11`), zero call sites in lib/test/examples. Fix: delete the block. Status: issue #14.
- F-34: stray `debug_me` statements fire every test run with no assertion purpose: `test/dispatcher_test.rb:98,110`, `test/subscribe_test.rb:21` (noise into the repo-root test log, pairs with F-13). Fix: delete the three lines. Status: issue #14.
- F-35: README link text typo "Documentation Websit" (missing "e") at `README.md:11`. Status: open, verified (trivial docs; bundle with a docs PR).
- F-36: public error classes `Errors::ReceivedMessageNotSubscribed`/`UnknownMessageClass` (`lib/smart_message/errors.rb:23,26`) are never raised in lib/test/examples (only echoed in `docs/core-concepts/architecture.md:363-364`). Same category as F-6 public-API orphans. Status: needs Dewayne's decision (remove as dead vs start raising vs reserve). In inbox.
- F-37: no version constraints on any runtime dependency (`smart_message.gemspec:39-48`), including the pre-1.0 `breaker_machines`/`chrono_machines` (0.x minors churn) and `activesupport` (no floor; see S-5). A downstream `bundle install` can resolve to a breaking 0.x or a vulnerable activesupport. Status: needs Dewayne's decision (pessimistic `~>` vs floors). In inbox.
- Note (C-3): `.travis.yml` (dead, targets Ruby 2.6.0) is tracked and would ship (F-7 missed it); PR #11 already deletes it. No separate finding.
- Note (C-4/deps re-check): new `bundle outdated` since last visit — `activesupport 8.1.4→8.1.4.1` (security, see S-5 local note), `prism 1.9.0→2.1.0` (major), `shoulda-matchers 4.5.1→8.0.1` (major, dev/test only). Known-still-outdated: breaker_machines 0.21.2→0.24.1, chrono_machines 0.11.0→0.12.1, hashie 5.1.0→5.2.0, minitest 6.0.6→6.1.0, redis-client 0.30.1→0.31.0. Dependabot alerts disabled; no `.github/dependabot.yml`.
- S-5 update: the activesupport advisories now have a shipped fix (`>= 8.1.4.1`), and the dep is a *runtime* dep declared unpinned (`gemspec:40`, loaded at `lib/smart_message.rb:14-19`), so this is broader than "dev lockfile only." Details held locally. Status: open (details in `notes/security/smart_message.md`).
