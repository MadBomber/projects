# Findings — smart_message-transport-lanet

Audit backlog for this repo. Numbered findings persist; statuses update as they become
issues (`issue #N`), PRs, or are resolved. Security details are held locally in
`notes/security/smart_message-transport-lanet.md`; only placeholders appear here.

## Audits table

| audit | last run | commit | result |
|-------|----------|--------|--------|
| security (+dead-code) | 2026-11-10 | c33acdf | S-1…S-6 (4 open, 1 theoretical, 1 dev-lockfile); F-1…F-13. F-1: the gem cannot run against the real `lanet` API |
| dependencies | 2026-11-12 | b9b206e | F-20…F-24 (+F-16 relock recipe verified on 4.0.9, clears S-6; F-24 floor needs decision); dead-code re-scan @ b9b206e: nothing new |
| adoption | — | — | not run yet |
| quality | — | — | not run yet (RuboCop baseline: 273 offenses, `rake` default task red) |

## 2026-11-10 — bomberbot — security + dead-code (first visit) — develop @ c33acdf

Dead-code scan reached develop @ c33acdf. bundle-audit DB @ bc80fbb (2026-11-09).

- **S-1:** security finding, details held locally. status: open (unreachable while F-1 stands).
- **S-2:** security finding, details held locally. status: open (unreachable while F-1 stands).
- **S-3:** security finding, details held locally. status: open (unreachable while F-1 stands).
- **S-4:** security finding, details held locally. status: open (becomes live with the obvious #3 fix; see local note).
- **S-5:** security finding, details held locally. status: unverified (theoretical).
- **S-6:** security finding, details held locally. status: open (dev lockfile only; fix is a relock).
- **S-7:** security finding, details held locally (reported by the #7 builder). status: open (unreachable while F-1 stands).

- **F-1:** The transport is written against a Lanet API that doesn't exist. `lanet.rb:154-157` calls
  `::Lanet::Sender.new(hash)` and `::Lanet::Receiver.new(hash) { … }`, `:204` calls `discover_nodes(timeout:)`,
  and `:233` and `:291` call `send_to_node(...)`. Real lanet (1.0.0 on RubyGems, and the 0.5.1 MadBomber fork)
  has `Sender.new(port)` with `send_to`/`broadcast`, `Receiver.new(port)` with `#listen`, and `Lanet::Mesh`,
  which has its own discovery. Nothing calls `listen`. Construction against real lanet raises
  `ReceiveError: no implicit conversion of Hash into String`. Published lanet 1.0.0 is also broken, because it
  leaves out `lanet/config.rb`. status: **needs Dewayne's decision**: rewrite on `Lanet::Mesh`, rewrite on
  Sender/Receiver, pin `< 1.0`, or mark the gem experimental. See inbox.md.
- **F-2:** The constructor runs before its state exists. Base 0.0.17 calls `configure` inside `initialize`, before
  `lanet.rb:47-54` set any state. `logger` is nil, so the rescue at `:161` raises NoMethodError; threads are
  orphaned; each `connect` adds another thread pair and another Sender/Receiver. status: **issue #3**.
- **F-3:** The tests require `ostruct` (`test_helper.rb:12`), so the suite won't load on Ruby 4.x and warns on
  3.4. status: **merged in [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5)** (also adds `require "minitest/mock"` for minitest 6).
- **F-4:** The test stubs mimic a Lanet API that doesn't exist, and the stub Base never calls `configure`, so the
  58 green tests can't catch F-1 or F-2. status: **needs Dewayne's decision** (tied to F-1).
- **F-5:** Subscription keys don't match. `subscribe` keys by the raw Class but inbound lookup uses `.to_s`
  (`:85`, `:308` vs `:359`, and `:94`), so the README's `subscribe(ChatMessage, …)` never receives.
  status: **issue #2**.
- **F-6:** You can't reconnect after `disconnect`: `@shutdown` is never reset (`:109`). status: **issue #4, PR open [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8)** (2026-11-12).
- **F-7:** `handle_smart_message` falls back to `message.to_s` when the payload is missing (`:354`). That passes
  the envelope's `inspect` string to `receive` and makes the guard at `:356` dead. status: open (small fix; moot
  until F-1).
- **F-8:** Documented config options are inert: `connection_timeout`, `max_message_size` and
  `enable_compression` are never read, `network_interface`/`encryption_key`/`signing_key` go only into a hash
  real lanet rejects, and the README's `capabilities:` option is never read either. status: **needs Dewayne's
  decision** (taste: implement, don't delete, as part of F-1).
- **F-9:** In `determine_target_nodes` (private), the `message_class` parameter is unused. Heartbeat
  `message_types` are stored but never used for routing. No private method is dead. status: open, low.
- **F-10:** The silent `rescue LoadError` around both requires (`:9-19`) hides that lanet 1.0.0 fails to load,
  and turns it into `uninitialized constant Lanet::Sender` later on. The deps are unconstrained in the gemspec.
  status: open. The version pin **needs Dewayne's decision** (gemspec), so decide it with F-1.
- **F-11:** Shared hashes are mutated by three threads with no mutex, yet the README claims "Thread-Safe
  Operations". status: unverified (a stress run raised no errors).
- **F-12:** There is no CI. status: **merged in [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6)** (2026-11-11) (CI green on 3.3 and 3.4; 3.2 dropped, see dewayne.md).
- **F-13:** The README links the MadBomber/lanet fork while the gemspec resolves RubyGems `lanet`
  (davidesantangelo). The gemspec also ships `.envrc` and `COMMITS.md`. status: open; the link depends on F-1,
  and the gemspec part needs Dewayne.

### Other verified items (phase 1, not audit)

- **F-14:** The README examples use SmartMessage APIs that don't exist in 0.0.17 (`attribute`, `message.header[:to]`,
  `transport.subscribe(Class, lambda)`) and never show how to bind the transport. status: **merged in
  [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7)** (2026-11-10).
- **F-15:** `bundle exec rake` (the default task: test plus RuboCop) fails with 273 offenses (`.rubocop.yml` is the
  bundler stub). status: open. Candidate next visit: a `.rubocop_todo.yml` baseline.
- **F-16:** The committed `Gemfile.lock` can't install on Ruby 4.x: unicode-emoji 4.1.0 needs Ruby < 4.0, and
  breaker_machines 0.7.0's native build fails. Triage verified that a relock is green on 4.0.9. status: open
  (relock PR candidate; also clears S-6).
- **F-17:** smart_message 0.0.17 fails to `require` (it needs `ap`, and its logger breaks with lumberjack 2). This
  is fixed on smart_message `develop` but not released. status: belongs to the smart_message repo; noted in inbox.
- **F-18:** Capability routing can never trigger: `determine_target_nodes` reads `header["capabilities"]`, but
  `SmartMessage::Header` has no such property, so the README lines `header[:capabilities] =` (about 143, 147, 272) would raise.
  status: open; fix in code as part of F-1 or F-8 (taste: code, not docs). Reported by the #7 builder.
- **F-19:** `publish_to_nodes` never delivers locally, so a node never receives its own published messages (the README Basic
  Setup suggests otherwise). status: open, **needs Dewayne decision** (is local delivery intended?).

## 2026-11-12 — bomberbot — dependencies (+dead-code) — develop @ b9b206e

Dead-code scan reached develop @ b9b206e (`lib/` unchanged since c33acdf; debride's 13 candidates are all public API or Base hooks; nothing new beyond F-9). bundle-audit DB @ 4d71b12 (2026-11-11).

- **F-16 (update):** still reproduces at b9b206e (`unicode-emoji-4.1.0 requires ruby version < 4.0`; breaker_machines 0.7.0's native build fails on 4.0.9). A naive relock on Ruby 4 picks breaker_machines ≥ 0.18 and chrono_machines ≥ 0.9.1, which need Ruby ≥ 4.0 and would break 3.3/3.4 CI. Verified recipe: temporarily pin breaker_machines 0.17.0 (and chrono_machines 0.8.0 if it moves), `bundle lock --conservative --update …`, unpin, `bundle lock`. Installs and passes on 4.0.9; clears S-6.
- **F-20:** `lanet.rb:4` requires `logger`, which isn't a default gem on Ruby 4.0, and the gemspec doesn't declare it; it loads only through transitive dependencies. Fix: `spec.add_dependency "logger"`. Low. Gemspec, so never self-merged. status: open.
- **F-21:** the Gemfile pins `minitest ~> 5.16` while 6.1.0 is current; with `~> 6.0` plus `gem "minitest-mock"` the suite runs green (58/299 on 4.0.9). Low. Sequence after F-16. status: open.
- **F-22:** the CI matrix leaves out Ruby 4.0, and its comment (`ci.yml:23-24`) blames unicode-emoji 4.1.0, a reason the F-16 relock removes. status: folded into the F-16 PR.
- **F-23:** blank numeric env vars become zero: `DEFAULT_CONFIG` (`lanet.rb:30-37`) uses `ENV['X']&.to_i || default`, and `""` isn't nil. `LANET_HEARTBEAT_INTERVAL=` gives `0.0`, so the heartbeat loop spins with `sleep(0)` and `cleanup_stale_nodes` evicts every node. Fix: a small helper that treats blank as unset. Low. status: open.
- **F-24:** smart_message's dependency chain (breaker_machines ≥ 0.18, chrono_machines ≥ 0.9.1) now needs Ruby ≥ 4.0. Keeping 3.3/3.4 means holding those gems back. The gemspec says `>= 3.2.0` and `.rubocop.yml` targets 3.2. status: **needs Dewayne's decision** (floor 3.3 or 4.0; see dewayne.md).

### Other verified items (phase 1, 2026-11-12)

- **F-25:** `test_configure_initializes_lanet_components` (`test_lanet.rb:65-79`) calls `configure` on a local transport and never disconnects it, so the real `Lanet-Discovery` and `Lanet-Heartbeat` threads outlive the suite (seen with `Minitest.after_run { Thread.list }`). Fix: `ensure transport.disconnect`, or build it through the setup/teardown `@transport`. status: open. (improver C1)
- **F-26:** the README's "Error Handling" section says failed node connections are "logged and retried", but `publish_to_nodes` (`lanet.rb:299-301`) only logs a warning. Nothing retries. status: **needs Dewayne's decision** (retry policy belongs with the F-1 rewrite; taste: change the code, not the docs).
- **F-27:** two tests in `test_lanet_concurrency.rb` (`:14-40`, `:309-359`) exercise a plain Hash under the GVL, not transport code, and so lend false support to the README's "Thread-Safe" claim (F-11). status: open; decide together with F-11. (improver C5)
- **F-28:** dead test setup: `test_lanet_concurrency.rb:6-12` assigns `@transport`, which no test reads, and `test_lanet_integration.rb:53` uses `puts` in a never-called handler. status: open, low. (improver C3)
- **F-29:** three near-identical `create_transport` helpers across the test files, each built with `allocate` + `instance_variable_set`. This bypasses `initialize`/`configure`, which is why F-2 stayed hidden. status: open, low. (improver C4; consolidate after #3)
