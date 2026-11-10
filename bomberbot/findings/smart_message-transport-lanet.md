# Findings — smart_message-transport-lanet

Audit backlog for this repo. Numbered findings persist; statuses update as they become
issues (`issue #N`), PRs, or are resolved. Security details are held locally in
`notes/security/smart_message-transport-lanet.md`; only placeholders appear here.

## Audits table

| audit | last run | commit | result |
|-------|----------|--------|--------|
| security (+dead-code) | 2026-11-10 | c33acdf | S-1…S-6 (4 open, 1 theoretical, 1 dev-lockfile); F-1…F-13. F-1: the gem cannot run against the real `lanet` API |
| dependencies | — | — | not run yet (S-6 and triage's `bundle outdated` give a head start) |
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
  3.4. status: **PR open [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5)** (also adds `require "minitest/mock"` for minitest 6).
- **F-4:** The test stubs mimic a Lanet API that doesn't exist, and the stub Base never calls `configure`, so the
  58 green tests can't catch F-1 or F-2. status: **needs Dewayne's decision** (tied to F-1).
- **F-5:** Subscription keys don't match. `subscribe` keys by the raw Class but inbound lookup uses `.to_s`
  (`:85`, `:308` vs `:359`, and `:94`), so the README's `subscribe(ChatMessage, …)` never receives.
  status: **issue #2**.
- **F-6:** You can't reconnect after `disconnect`: `@shutdown` is never reset (`:109`). status: **issue #4**.
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
- **F-12:** There is no CI. status: **PR open [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6)** (CI green on 3.3 and 3.4; 3.2 dropped, see dewayne.md).
- **F-13:** The README links the MadBomber/lanet fork while the gemspec resolves RubyGems `lanet`
  (davidesantangelo). The gemspec also ships `.envrc` and `COMMITS.md`. status: open; the link depends on F-1,
  and the gemspec part needs Dewayne.

### Other verified items (phase 1, not audit)

- **F-14:** The README examples use SmartMessage APIs that don't exist in 0.0.17 (`attribute`, `message.header[:to]`,
  `transport.subscribe(Class, lambda)`) and never show how to bind the transport. status: **PR open
  [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7)**.
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
