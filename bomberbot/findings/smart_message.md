# Findings: smart_message

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-12 | 2nd run, at develop 07af3d4 (0.1.0 rewrite). bundler-audit: 2 activesupport advisories, lockfile only (transitive via breaker_machines/shoulda-matchers). No shell-outs, YAML or Marshal in lib/bin/Rakefile; one ENV read (DLQ path). S-1, S-3, S-4 fixed by the rewrite; S-2 now documented design; new S-8..S-12 held locally (S-8, S-9 probe-verified against Redis); F-60 Redis subscriber reliability. (1st run 2026-11-10 at 9371456.) |
| dead code | 2026-11-12 | Full run at develop 07af3d4 (lib, bin, examples incl. city_scenario/performance_metrics, tests, docs). debride + reference/constant/orphan scans; dynamic refs (string handlers, send, const_get, Zeitwerk) checked; dependents freshly cloned (transport-lanet/named_pipes, nats/rabbitmq/email, smart_message_demos): none use any candidate. F-67 unchanged; new F-71..F-77 (F-71..F-74 removals proven 443/1750/0F, examples 21/21; F-75/F-76 need decision). |
| dependencies | 2026-11-10 (3rd visit) | First run, at develop 9371456. Suite green on 4.0.9 (current lock), 4.1 (all gems updated), and with breaker_machines 0.13.2. Zeitwerk eager-load NameError on DDQ (F-38, issue #15); undeclared bundled gem `logger` (F-39); lock bump clean (F-40, after #11); beta bundler pin (F-41); unused mutex_m and shoulda→shoulda-context (F-42/43); F-10 floor evidence (lib parses at 3.3); optional redis (F-44). No lib deprecation warnings under -W:deprecated; suite passes with frozen string literals forced on. |
| adoption | 2026-11-10 (2nd visit) | First run, at develop 9371456. `mkdocs build --strict` clean; memory examples run. `:redis_queue` transport + `examples/redis_queue/` + `docs/transports/redis-queue.md` all documented but nonexistent (F-24/25/26, needs decision); FileTransport unregistered so `create(:file)`→nil (F-27, issue #12); README `Serializer::JSON` casing NameError (F-28); CHANGELOG/tag drift + dup `[0.0.1]` (F-29/30); no llms.txt (F-31). Broken `<string>` docs homepage tag (F-32, issue #13); dead test debug code (F-33/34, issue #14). Obvious-yes: F-27, F-30, F-32, F-33/34. |
| quality | 2026-11-10 (3rd visit, brief) | flog/flay/rubocop Lint (default config); reek not run. Flaky timing test (F-48); triplicated transport `logger` (F-49); dup circuit-stats hash (F-50); dead branch (F-51); empty metaclass (F-52). Hotspots: Messaging#publish 117.6, Base#initialize 98.9, Dispatcher#route 92.6. Improver read dispatcher/DDQ/circuit breaker: F-53–F-59. |

Dead-code scan last covered commit: 07af3d4 (develop, 2026-11-12; full)

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

### 2026-11-10 (third visit; dependencies audit + brief quality pass + improver, develop 9371456)

- F-38: gem cannot be eager-loaded: `ddq.rb` defines `SmartMessage::DDQ` but `Zeitwerk::Loader.for_gem` (`lib/smart_message.rb:24-27`) has no inflection, so `Zeitwerk::Loader.eager_load_all` raises `NameError: uninitialized constant SmartMessage::Ddq` (breaks a Rails production boot). Fix: `loader.inflector.inflect("ddq" => "DDQ")` + eager-load test; verified green in scratch. Also found by the improver. Status: issue #15.
- F-39: `logger` is required (`logger/default.rb:5`) but undeclared; it's a bundled (not default) gem since Ruby 4.0 and resolves only transitively. Fix: `spec.add_dependency 'logger'`. Status: open, verified (gemspec PR, not low risk).
- F-40: `bundle update --all` (breaker_machines 0.24.1, chrono_machines 0.12.1, activesupport 8.1.4.1, hashie 5.2.0, minitest 6.1.0, prism 2.1.0) keeps the suite at 316/940/0F/0E/27S; redis-client is pinned by redis 6.0.0. chrono_machines 0.12.1 adds `rb_sys`. Status: open, verified; wait for #11 (also edits `Gemfile.lock`).
- F-41: `Gemfile.lock` says `BUNDLED WITH 4.1.0.beta2`; Ruby 4.0 contributors and CI auto-install a prerelease bundler. Status: needs Dewayne's decision (his local Ruby would rewrite it).
- F-42: dev dependency `mutex_m` (`gemspec:53`) unused anywhere. Status: open, verified (pair with F-43).
- F-43: dev dependency `shoulda` can be `shoulda-context` (tests use no matchers); drops the shoulda-matchers 4.5.1 pin. Verified green. Status: open, verified.
- F-10 update: lib parses under the 3.3 grammar and the suite is green with breaker_machines 0.13.2, so the coherent floors are `>= 3.3` or `>= 4.0`; `>= 3.0.0` is wrong either way. Still needs Dewayne's decision.
- F-44: `redis` is a hard runtime dep but only loaded lazily (`transport/redis_transport.rb:5`, `ddq/redis.rb:143`). Same decision as F-5. Status: needs Dewayne's decision.
- F-45: every require loads `active_support/core_ext/object/try` + `log_subscriber` (`lib/smart_message.rb:15-19`) only for dev-only `amazing_print`. Status: needs Dewayne's decision (comment says deliberate).
- F-46: Ruby 4 cosmetic cleanup: redundant `require 'set'` (3 files), misplaced `# encoding: utf-8` in 42 files, `lib/simple_stats.rb` missing frozen_string_literal. Status: open, low value.
- F-47: `-w` warnings: indentation mismatch `transport/stdout_transport.rb:16`, unused `e` `transport/file_operations.rb:95`. Status: open, trivial.
- F-48: flaky wall-clock test `test_concurrent_proc_handler_execution` (`test/proc_handler_integration_test.rb:362`), failed 2/5 full runs. Fix: assert overlap, not finish order; rule out a pool cause first. Status: open, verified (test-only, obvious yes).
- F-49: three identical private `logger` methods in FileTransport mixins (`async_publish_queue.rb:14`, `fifo_operations.rb:14`, `file_watching.rb:12`) override `Transport::Base#logger`. Status: open, verified.
- F-50: duplicated circuit-stats hash (`dispatcher.rb:269`, `transport/base.rb:134`). Status: open, low.
- F-51: both branches of `Configuration#default_logger` (`configuration.rb:126-132`) return `Logger::Null.new`. Status: open, trivial.
- F-52: empty `class << self` with a TODO (`dispatcher.rb:524-526`). Status: open, trivial.
- F-53: `enable_deduplication!` on a subclass leaves dedup silently off: defaults are set only on `Base` (`deduplication.rb:13-20`), subclass `ddq_config` is all nil, `dispatcher.rb:458` rescues the nil `.merge`. Status: issue #16.
- F-54: Redis DDQ never evicts from its lookup SET: `LTRIM` runs in `MULTI` before the `llen > size` check (`ddq/redis.rb:52-74`); size 3 + adds a..e → `contains?("a")` true, count 5. Memory backend correct. Fix: LPUSH, read overflow via LRANGE, SREM, LTRIM. Status: open, verified by improver (needs Redis).
- F-55: `CircuitBreaker.stats`/`.available?` (`circuit_breaker.rb:129-150`) always raise NoMethodError (`state` etc. not on BreakerMachines::Circuit). Status: issue #17.
- F-56: Redis DDQ ignores documented `redis_url:`/`redis_db:` (`ddq/redis.rb:18,141-144`; docs `README.md:306`, `message-deduplication.md:102-104`); silently connects to localhost db 0. Status: open, verified by improver.
- F-57: `Dispatcher#route` nil check on `@subscribers[klass]` (`dispatcher.rb:204`) can never fire (hash default block) and inserts an empty entry per routed unsubscribed class; "disoatcher" typo at `:10`. Status: open, low.
- F-58: public `Dispatcher#worker_task_completed` (`dispatcher.rb:71-73`) looks like a getter but increments the pool's completed counter; no callers. Status: needs Dewayne's decision (remove vs deprecate).
- F-59: undocumented debug helpers `Dispatcher#pool` (instance_variable_get), `#what_can_i_do?`, and `#status`'s dead NoMethodError fallback (`dispatcher.rb:43-64`). Status: needs Dewayne's decision (keep vs remove with F-58).
- Note: no new security findings; S-1..S-7 unchanged. F-40's lock bump would move activesupport past S-5's versions, but S-5's real fix is the unpinned runtime dep.

### 2026-11-12 (fourth visit; security audit + triage/improver re-check, develop 07af3d4)

Dewayne's 0.1.0 rewrite (07af3d4) landed between visits. Re-check at 07af3d4:

- **Fixed by 07af3d4:** F-1, F-2, F-3, F-4, F-5, F-7, F-8, F-10 (`>= 4.0`), F-11, F-12, F-13/F-23 (per-run tmpdir), F-14, F-17, F-18, F-19, F-20, F-22, F-27, F-37, F-38, F-45, F-49, F-52, F-53, F-54/F-56 (Redis DDQ rewritten), F-55, F-57, F-58, F-59. Issues #6, #7, #8, #12, #13, #15, #16, #17 fixed on develop (BomberBot commented on each; Dewayne to close).
- **Partly fixed:** F-6 (`configure_entity_circuit`, `graceful_degradation` remain); F-33/F-34 / #14 (monkeypatch gone, three stray `debug_me` calls and a dead `DebugMeDefaultOptions` block remain; PR this visit); F-36 (`ReceivedMessageNotSubscribed` never raised).
- **Still open:** F-30 (dup `[0.0.1]`, now `CHANGELOG.md:712`/`:758`), F-39, F-41, F-42, F-43, F-44, F-46 (`require 'set'` in 2 files), F-48 (passed 5/5 today), MathJax/macros unused in `mkdocs.yml`.
- S-1: fixed (residual path = S-8). S-2: still true, now documented design; needs Dewayne's decision (close, or opt-in validation on receive). S-3, S-4: fixed. S-5: narrowed to the dev lockfile (activesupport now transitive). S-6: FIFO part gone; remainder documented design. S-7: fixed on develop (#9) but `main` still serves the old docs config until a release; needs Dewayne.
- S-8: security finding, details held locally. Status: PR'd (link held locally).
- S-9: security finding, details held locally. Status: open (small-PR candidate).
- S-10: security finding, details held locally. Status: needs Dewayne's decision.
- S-11: security finding, details held locally. Status: needs Dewayne's decision.
- S-12: security finding, details held locally. Status: open (small-PR candidate).
- F-60: `RedisTransport` subscriber thread exits for good after `reconnect_attempts` failed reconnects (`transport/redis_transport.rb` `subscriber_loop`), while `connected?` pings only the publish connection and stays true. Status: issue [#18](https://github.com/MadBomber/smart_message/issues/18).
- F-61: `test_dispatcher_routing` cannot fail (processors `puts` on mismatch, never assert); `test/test_helper.rb:47-58` `DebugMeDefaultOptions` block is dead (debug_me 1.1.3 defines the constant). Status: PR'd with #14 (see runs.md).
- F-62: `docs/reference/dead-letter-queue.md:119-121` says replay calls `publish` and a failed replay writes a new entry; code (`dead_letter_queue.rb:365-382`) replays directly and keeps the one entry. Status: PR'd (see runs.md).
- F-63: no LICENSE file and no `spec.license`, though README says MIT; `gem build` warns. Status: issue [#19](https://github.com/MadBomber/smart_message/issues/19).
- F-64: `subscribe` accepts unresolvable String/Symbol handlers; failure only shows as a DLQ entry. Status: issue [#20](https://github.com/MadBomber/smart_message/issues/20).
- F-65: `.github/workflows/test.yml` runs only `rake test`, not `rake examples` (21/21 pass locally in ~10 s). Status: open, small-PR candidate (CI file: not low risk; push needs `workflow` scope).
- F-66: `activesupport` 8.1.4 → 8.1.4.1 in `Gemfile.lock` via `bundle update --conservative activesupport`; suite green. Status: open, small-PR candidate (lockfile: not low risk).
- F-67: public API with no callers anywhere, cheapest to drop before 0.1.0 is published: `InheritableSettings#setting?`, `required_property_names`, `validate_all`, `property_validator`, `Subscription.call_proc_handler`, `Versioning#validate_header_version!`, `Dispatcher#drop_all!`, `Errors::ReceivedMessageNotSubscribed`. Status: needs Dewayne's decision.
- F-68: DLQ replay publishes to every transport of the class, including the ones that already succeeded (`dead_letter_queue.rb:376` ignores the entry's `transport`). Status: needs Dewayne's decision (how to identify transport instances).
- F-69: unknown transport options are silently ignored (`Transport.create(:memory, bogus: 1)`); a `namspace:` typo would merge environments. Status: open, idea (inbox).
- F-70: dev tooling: no RuboCop config, Dependabot alerts disabled, `hashie` 6.0.0 blocked by `~> 5.0`; chrono_machines 0.12.x adds a native extension. Status: needs Dewayne's decision (lock bump scope).

### 2026-11-12 (fifth visit; dead-code audit at develop 07af3d4, improver)

- F-71: two test helpers are never called: `DeadLetterQueueTest#create_header` (`test/dead_letter_queue_test.rb:243-251`) and `MultiTransportTest::CountingTransport#reset_counters` (`test/multi_transport_test.rb:64-67`). Removal proven (443/1750/0F). Status: open, obvious-yes test-only PR (after #23, which edits the same test file).
- F-72: `require 'minitest/power_assert'` and `'minitest/mock'` (`test/test_helper.rb:36-37`) are unused (no `assert { }` blocks, no mocks or stubs); suite identical without them. Status: open, obvious-yes test-only PR. Dropping the two gemspec dev deps (`smart_message.gemspec:54-55`) belongs with the F-42/F-43 gemspec batch.
- F-73: `RedisStats#build_performance_stats`, `#build_message_breakdown`, `#build_memory_stats` (`examples/city_scenario/redis_stats.rb:441-499,535-550`, 77 lines) are never called; `render_dashboard` uses the other seven builders. Dead on `main` too. Status: open, obvious-yes examples-only removal (ask "or wire them in?" in the PR).
- F-74: `RedisExample.failures` (`examples/redis/support.rb:86-88`), `PerformanceBenchmark#results` reader (`examples/performance_metrics/performance_benchmark.rb:66`) unused; `examples/city_scenario/city_diagram.svg` referenced by nothing. Status: open, bundle with F-73.
- F-75: public API with zero callers anywhere (adds to F-67): `Dispatcher#shutdown_pool` alias (`dispatcher.rb:308`), `Dispatcher#proc_handler?` (`:199-202`), instance `Plugins#config` (`plugins.rb:41-43`), `Plugins#transport_configured?` and `ClassMethods#transport_configured?` (`plugins.rb:59,92`), `RedisTransport#redis_pub` reader (`redis_transport.rb:43`). Status: needs Dewayne's decision (drop before 0.1.0 ships and list them in the upgrade guide, or keep).
- F-76: public API used only by its own tests, undocumented: `Dispatcher#drop_subscriber`, `Versioning::ClassMethods#expected_header_version`, `Transport::Registry#registered?`, `DDQ::Base#add`, `CircuitBreaker.configure_entity_circuit` (rest of F-6). `graceful_degradation`, `configure_for` and `available?` are now documented and live. Status: needs Dewayne's decision (document or remove).
- F-77: documented API with no test: `DeadLetterQueue#replay_batch` (`dead_letter_queue.rb:162-164`), `DeadLetterQueue.configure_default` (`:41-44`), `Plugins::ClassMethods#reset_logger` (`plugins.rb:111`). Status: open, obvious-yes test-only PR (after #23).
- F-78: payloads that raise a plain `ArgumentError` while decoding (bad header `published_at`, raising `coerce_key`) skip the DLQ (`transport/base.rb:184` rescues only `Errors::Error`); Redis logs them at debug only. Status: issue [#24](https://github.com/MadBomber/smart_message/issues/24).
- F-79: `config.logger = Pathname(...)` or `:stdout` is accepted and fails later with NoMethodError (`configuration.rb:148-157`); Lumberjack doesn't create a Pathname log dir. Status: issue [#25](https://github.com/MadBomber/smart_message/issues/25).
- F-80: `transport :memory` (a Symbol) is accepted at class definition and fails at subscribe/publish, writing a junk DLQ entry (`plugins.rb:46-50,79-84`). Status: issue [#26](https://github.com/MadBomber/smart_message/issues/26).
- F-81: `p2p_plan.md` / `p2p_roadmap.md` (612 lines, repo root) are unlinked strategy notes; `Errors::ReservedPropertyName < ArgumentError` escapes `rescue Errors::Error`. Status: idea (inbox).
- Security: the improver independently re-found S-11 (held locally); nothing new.
