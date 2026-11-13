# Findings — smart_message-transport-lanet

Audit backlog for this repo. Numbered findings persist; statuses update as they become
issues (`issue #N`), PRs, or are resolved. Security details are held locally in
`notes/security/smart_message-transport-lanet.md`; only placeholders appear here.

## Audits table

| audit | last run | commit | result |
|-------|----------|--------|--------|
| security (+dead-code) | 2026-11-13 | 3b0abb9 | bundle-audit clean (DB 4d71b12 = upstream HEAD); S-6 fixed by #13; S-1…S-5, S-7, S-8 still reproduce (all unreachable while F-1 stands); #8/#9 add no exposure; no new F/S. Earlier run: 2026-11-10 @ c33acdf (S-1…S-6, F-1…F-13). dead-code @ 3b0abb9: nothing new |
| dependencies (+dead-code) | 2026-11-13 | 3b0abb9 | F-55, F-56 (+F-15 PR-ready → #32; F-21 lock-only change clean against all of #20–#29, 64/307 on 4.0.9; F-18 0.1.0 note). Lanet 1.0.0 / smart_message 0.0.17 still latest releases; 0.1.0 unreleased on develop. Lock green on 4.0.9 + 4.1.0-preview1. Earlier run: 2026-11-12 @ b9b206e (F-20…F-24). Dead-code @ 3b0abb9: nothing new |
| adoption | 2026-11-12 | b9b206e | F-30…F-34 (+F-13 update); README runs on 0.0.17 with F-2/F-10/F-17 workarounds except F-18; no mkdocs/examples/llms.txt; gem-skill fetch-only; dead-code re-scan @ b9b206e: nothing new |
| quality | 2026-11-12 | b9b206e | F-35…F-38, S-8 (+F-11 verified → #18; F-15 todo recipe verified; F-2/F-9/F-29/F-30 notes). RuboCop 273 (lib 113), reek 43, flog 342 / 13.7 per method, flay 852 (tests only); dead-code re-scan @ b9b206e: nothing new |

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
  status: **issue #2, PR open [#9](https://github.com/MadBomber/smart_message-transport-lanet/pull/9)** (2026-11-12).
- **F-6:** You can't reconnect after `disconnect`: `@shutdown` is never reset (`:109`). status: **issue #4, PR open [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8)** (2026-11-12).
- **F-7:** `handle_smart_message` falls back to `message.to_s` when the payload is missing (`:354`). That passes
  the envelope's `inspect` string to `receive` and makes the guard at `:356` dead. status: **issue [#15](https://github.com/MadBomber/smart_message-transport-lanet/issues/15)** (2026-11-12; fail-before test verified by the improver).
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
  Operations". status: **issue [#18](https://github.com/MadBomber/smart_message-transport-lanet/issues/18)** (verified 2026-11-12, see the quality audit).
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

- **F-16 (update):** status: **PR open [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13)**. Still reproduces at b9b206e (`unicode-emoji-4.1.0 requires ruby version < 4.0`; breaker_machines 0.7.0's native build fails on 4.0.9). A naive relock on Ruby 4 picks breaker_machines ≥ 0.18 and chrono_machines ≥ 0.9.1, which need Ruby ≥ 4.0 and would break 3.3/3.4 CI. Verified recipe: temporarily pin breaker_machines 0.17.0 (and chrono_machines 0.8.0 if it moves), `bundle lock --conservative --update …`, unpin, `bundle lock`. Installs and passes on 4.0.9; clears S-6.
- **F-20:** `lanet.rb:4` requires `logger`, which isn't a default gem on Ruby 4.0, and the gemspec doesn't declare it; it loads only through transitive dependencies. Fix: `spec.add_dependency "logger"`. Low. Gemspec, so never self-merged. status: **issue [#11](https://github.com/MadBomber/smart_message-transport-lanet/issues/11)**.
- **F-21:** the Gemfile pins `minitest ~> 5.16` while 6.1.0 is current; with `~> 6.0` plus `gem "minitest-mock"` the suite runs green (58/299 on 4.0.9). Low. Sequence after F-16. status: open.
- **F-22:** the CI matrix leaves out Ruby 4.0, and its comment (`ci.yml:23-24`) blames unicode-emoji 4.1.0, a reason the F-16 relock removes. status: folded into **PR open [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13)**.
- **F-23:** blank numeric env vars become zero: `DEFAULT_CONFIG` (`lanet.rb:30-37`) uses `ENV['X']&.to_i || default`, and `""` isn't nil. `LANET_HEARTBEAT_INTERVAL=` gives `0.0`, so the heartbeat loop spins with `sleep(0)` and `cleanup_stale_nodes` evicts every node. Fix: a small helper that treats blank as unset. Low. status: **issue [#10](https://github.com/MadBomber/smart_message-transport-lanet/issues/10)**.
- **F-24:** smart_message's dependency chain (breaker_machines ≥ 0.18, chrono_machines ≥ 0.9.1) now needs Ruby ≥ 4.0. Keeping 3.3/3.4 means holding those gems back. The gemspec says `>= 3.2.0` and `.rubocop.yml` targets 3.2. status: **needs Dewayne's decision** (floor 3.3 or 4.0; see dewayne.md).

### Other verified items (phase 1, 2026-11-12)

- **F-25:** `test_configure_initializes_lanet_components` (`test_lanet.rb:65-79`) calls `configure` on a local transport and never disconnects it, so the real `Lanet-Discovery` and `Lanet-Heartbeat` threads outlive the suite (seen with `Minitest.after_run { Thread.list }`). Fix: `ensure transport.disconnect`, or build it through the setup/teardown `@transport`. status: **issue [#12](https://github.com/MadBomber/smart_message-transport-lanet/issues/12)**. (improver C1)
- **F-26:** the README's "Error Handling" section says failed node connections are "logged and retried", but `publish_to_nodes` (`lanet.rb:299-301`) only logs a warning. Nothing retries. status: **needs Dewayne's decision** (retry policy belongs with the F-1 rewrite; taste: change the code, not the docs).
- **F-27:** two tests in `test_lanet_concurrency.rb` (`:14-40`, `:309-359`) exercise a plain Hash under the GVL, not transport code, and so lend false support to the README's "Thread-Safe" claim (F-11). status: open; decide together with F-11. (improver C5)
- **F-28:** dead test setup: `test_lanet_concurrency.rb:6-12` assigns `@transport`, which no test reads, and `test_lanet_integration.rb:53` uses `puts` in a never-called handler. status: open, low. (improver C3)
- **F-29:** three near-identical `create_transport` helpers across the test files, each built with `allocate` + `instance_variable_set`. This bypasses `initialize`/`configure`, which is why F-2 stayed hidden. status: open, low. (improver C4; consolidate after #3)

## 2026-11-12 — bomberbot — adoption (+dead-code) — develop @ b9b206e

Dead-code scan reached develop @ b9b206e (`lib/` unchanged since c33acdf; debride's 13 candidates are the same public API / Base hooks; nothing new). README examples were run on Ruby 4.0.9 against real smart_message 0.0.17, with scratch-only workarounds for F-17 (`ap` stub, plain logger), F-10/F-1 (Lanet stand-in) and F-2 (configure/logger shim). Everything the README shows works except `header[:capabilities]=` (F-18). No mkdocs, no examples/. gem-skill: fetch-only (generation sends content to an LLM).

- **F-13 (update):** the built gem now also ships `.github/workflows/ci.yml` (added by #6), alongside `.envrc` and `COMMITS.md`. Still gemspec, still Dewayne's call.
- **F-30:** the default logger can't be built. `lanet.rb:48` `Logger.new($stdout)` resolves to `SmartMessage::Logger`, a module with no `.new`, so any `Lanet.new` without `logger:` raises `NoMethodError`. Hidden today by F-2; the stub Base sets `@logger` itself. The README's "Logging" section also says the transport uses the SmartMessage logger, which it ignores. Fix: default to `SmartMessage::Logger.default` plus a regression test; build with or after #3 and check the local security note first (don't use `::Logger.new`). status: **issue [#14](https://github.com/MadBomber/smart_message-transport-lanet/issues/14)**.
- **F-31:** the README's install steps don't work: RubyGems has no such gem (API 404), no tags, no releases. status: **needs Dewayne's decision** (publish 0.0.1, or a "not yet on RubyGems; use `github:`" note until F-1).
- **F-32:** the CHANGELOG's `## [0.0.1] - 2025-09-03` is the bundler scaffold placeholder, dated before the first commit, never tagged or published, and omits the real implementation. status: **needs Dewayne's decision** (decide with F-31).
- **F-33:** gemspec metadata gaps: `gem build` warns that `homepage_uri` and `source_code_uri` duplicate; no `bug_tracker_uri`, `documentation_uri` or `rubygems_mfa_required`; `changelog_uri` mis-indented. status: **issue [#16](https://github.com/MadBomber/smart_message-transport-lanet/issues/16)** (gemspec, never low risk).
- **F-34:** no runnable example and no `llms.txt`; gem-skill's fetcher gets `examples: nil`. status: open, sequenced after F-1 (would document a transport that can't run).
- **F-28 (update):** the `test_lanet_concurrency.rb:6-13` half is a ready −8-line test-only removal (improver C3: no reader of `@transport` in that file; teardown can never fire; suite stays 58/299). The `puts` half waits for #9.

## 2026-11-12 — bomberbot — quality (+dead-code) — develop @ b9b206e

Dead-code scan reached develop @ b9b206e (`lib/` unchanged since c33acdf; debride's 13 candidates unchanged; nothing new). Tools: RuboCop 1.81.7 with repo config (273 offenses: lib 113, rest test/gemspec), reek 6.6 (43 warnings on lib), flog (342 total, 13.7 per method; worst `perform_node_discovery` 36.3, CC 11), flay (852, all in tests). Repros on Ruby 4.0.9 + real smart_message 0.0.17 with scratch-only F-17/F-1 shims.

- **S-8:** security finding, details held locally. status: open (verified; unreachable while F-1 stands). (The auditor's other security candidate duplicated S-7; the local note was updated.)
- **F-35:** `unsubscribe!` leaves the Lanet handler registered: Lanet overrides `subscribe`/`unsubscribe` but not `unsubscribe!`, so `Base#unsubscribe!` (smart_message 0.0.17 `transport/base.rb:100`) drops the dispatcher entry but the handler stays in `@message_handlers`; heartbeats keep advertising the type (`lanet.rb:227`) and inbound messages still reach `receive` (`:359`). The test stub Base has no `unsubscribe!`. Fix: override → `super` + `unregister_message_handler`, ~10 lines, after #9 merges. status: **issue [#17](https://github.com/MadBomber/smart_message-transport-lanet/issues/17)**. (Same as the repo-file backlog note "from #9".)
- **F-36:** `disconnect` returns while its threads are still alive: `lanet.rb:112-113` `Thread#kill` without `join`; alive right after `disconnect` in 200/200 runs. Fix: `&.join(1)` after each kill, plus a test, ~4 lines, after #8 merges. status: open, low; already listed among #3's deferred lifecycle items, so not filed separately.
- **F-37:** indifferent-key lookups `h[:k] || h['k']` 13× (`lanet.rb:207,211,212,214,320,326,334,339,340,342,343,353,354`) drive the flog/AbcSize hotspots and reek FeatureEnvy/DuplicateMethodCall. Fix: one private `field(hash, key)` helper. status: open, low; fold into the F-1 rewrite (the hash shapes change anyway).
- **F-38:** three concurrency tests can't detect lost writes: `test_lanet_concurrency.rb:73` (`>= 1` after 20 distinct heartbeats), `:97` (`>= 1` after 5 handlers), `:358` (`> 0`). Fix: exact counts, ~3 test lines; a constant-key mutation of `handle_heartbeat` stays green today. status: **issue [#19](https://github.com/MadBomber/smart_message-transport-lanet/issues/19)**. (improver C3)
- **F-11 (update):** verified. `cleanup_stale_nodes` (`:243`, `reject!` with a logging block) racing `perform_node_discovery` inserts (`:210`) raises `can't add a new key into hash during iteration` in the discovery thread; the round's remaining nodes are dropped. 97,250 failures in 120,167 rounds over 5 s at `:debug`. Fix: one Mutex (medium; belongs with F-1/F-27). status: issue #18.
- **F-15 (update):** recipe verified on a scratch copy: `rubocop --auto-gen-config --no-exclude-limit` (222-line `.rubocop_todo.yml`) leaves 1 offense (`test_lanet_integration.rb:317` `Thread.new { }`); adding `EnforcedStyleForEmptyBraces: space` under `Layout/SpaceInsideBlockBraces` in the todo makes RuboCop exit 0, suite 58/0 on 4.0.9. Generate after #8/#9 merge. Never autocorrect `Style/HashEachMethods` at `lanet.rb:231` (`keys.each` is a deliberate snapshot).
- **F-29 (update):** flay's top hit (mass 576) is the same two-node `@discovered_nodes` fixture copied 6× (`test_lanet.rb:124,137,150`, `test_lanet_integration.rb:28,162,176`); `test_lanet_integration.rb:399` is a `Lint/Void`. Also `test_initialization_with_custom_options` (`test_lanet.rb:30-37`) can't fail (uses `allocate`, asserts only `instance_of`). `test_lanet_integration.rb:366-369` duplicates `test_lanet.rb:14-16`.
- **F-9 (update):** `handle_smart_message`'s `sender_info` (`:352`) is also unused.
- **F-2/F-30 (note):** the constructor is written for the stub: `defined?(super)` (`:46`) is always true; `@options ||=` (`:47`) is a no-op after `Base#initialize`; `attr_reader :options` duplicates Base's; `attr_reader :logger` makes public and shadows Base's private `logger`, which already defaults to `SmartMessage::Logger.default`. Dropping that reader and the `Logger.new` fallback may be the smallest F-30 fix (public API: see inbox).
- **Note (improver):** `connected?` (`:96`) returns `nil` rather than `false` when `@sender` is nil; `!!(…)` plus a test, after #8 merges. Low.

## 2026-11-13 — bomberbot — security (+dead-code) — develop @ 3b0abb9

Dead-code scan reached develop @ 3b0abb9 (`lib/` changed only by #8/#9; debride's 13 `lib/` candidates are unchanged public API / Base hooks; #9's new test helpers are all used; nothing new beyond F-9/F-28). bundle-audit 0.9.3, DB 4d71b12 (= upstream HEAD, 1359 advisories): "No vulnerabilities found". Suite green on 4.0.9 (64 runs / 303 assertions). No new audit findings; F-39…F-49 and S-9+ unused.

- **S-1, S-2, S-3, S-7, S-8:** security findings, details held locally. status: open (re-verified at 3b0abb9; unreachable while F-1 stands).
- **S-4:** security finding, details held locally. status: open (re-checked against Dewayne's PR #29; see local note).
- **S-5:** security finding, details held locally. status: unverified (theoretical; unchanged).
- **S-6:** security finding, details held locally. status: **fixed by [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13)**.
- **F-1 / F-10 (re-verified):** lanet 1.0.0 still can't load (`lanet/receiver.rb:4` requires an unshipped `config`), and the real `Sender`/`Receiver` take a port, not a config hash. Every network path stays unreachable.
- **F-4 (note):** the stub `Base#subscribe`/`#unsubscribe` are no-ops, so #9's unsubscribe tests build the dispatcher's subscribers hash by hand (`test_lanet_integration.rb:423-436`).
- **F-15:** re-verified: 270 offenses in 6 files; `bundle exec rake` exits 1. status: **issue [#31](https://github.com/MadBomber/smart_message-transport-lanet/issues/31)**.
- **F-36:** re-verified: both threads alive right after `disconnect` in 50/50 runs. Dewayne's #29 moves the kills into `stop_services` without a join. status: **issue [#30](https://github.com/MadBomber/smart_message-transport-lanet/issues/30)**.
- **F-21 (update):** minitest `~> 6.0` + `minitest-mock` resolve to 6.1.0 / 5.27.0 and the suite is green on 4.1.0-preview1 (triage, scratch). Wait for #22 (lockfile).
- **#3 (update, responder):** #8 made the orphaned-thread symptom worse: a quick `disconnect` + `connect` now resets `@shutdown` before the constructor's orphaned threads wake, leaving 2 discovery + 2 heartbeat threads (b9b206e: 0). Dewayne's #29 addresses it.
- **#17 (update, responder):** after #9, `unsubscribe!(ChatMessage)` leaves Class subscriptions still reaching `receive` (before #9 they never received at all). #26 fixes it; its test checks only `active_subscriptions`.

### Review notes on Dewayne's PRs #20–#29 (improver + main session, 2026-11-13)

- **F-50:** seven of them (#22, #23, #25–#29) add a CHANGELOG line at the same spot; every pair conflicts there. #20, #21, #24 conflict with nothing. Main-session merge simulation in numeric order with a union merge for CHANGELOG: #20–#28 apply cleanly and the suite is green (70 runs); #29 then conflicts in `lanet.rb` and `test_helper.rb`.
- **F-51:** #27 and #29 both rewrite `initialize`. #29 keeps `Logger.new($stdout)`, which inside the class resolves to `SmartMessage::Logger` (no `.new` in the real gem), so #29 alone doesn't fix construction with the real gem. Merge #27 first; on rebase #29's line becomes `SmartMessage::Logger.default` before `super`. status: inbox (Dewayne's PRs).
- **F-52:** #28 and #29 conflict in `disconnect` (adjacent hunks). Also `@nodes_mutex = Mutex.new` must stay before `super` after #29, because `Base#initialize` starts the threads; the stub suite wouldn't catch it.
- **F-53:** #25 adds a public `self.env_number`; `private_class_method :env_number` would keep it off the API. Low.
- **F-54:** #28 makes `discovered_nodes`/`node_registry` return copies (documented in its CHANGELOG). Note, not a defect.
- Suggested merge order (improver): #21, #20, #24, #22, #23, #25, #26, #27, #29, #28.

## 2026-11-13 — bomberbot — dependencies (+dead-code, visit 6) — develop @ 3b0abb9

Dead-code scan reached develop @ 3b0abb9 (debride's 13 `lib/` candidates unchanged: public API / Base hooks; no `exe/`; nothing new beyond F-9/F-28). Latest releases: lanet 1.0.0 (unchanged; the MadBomber fork is untagged at 0.5.1), smart_message 0.0.17 (0.1.0 exists only on its `develop`, unreleased, `required_ruby_version >= 4.0`), minitest 6.1.0, rake 13.4.2, rubocop 1.92.2. `bundle outdated`: minitest is the only direct dependency held back by a constraint (F-21). The committed lock installs and passes on 4.0.9 and 4.1.0-preview1 (64/303, no deprecation output). breaker_machines ≥ 0.21's `< 4.1.dev` cap applies only to precompiled builds.

- **F-55:** gemspec leaves `smart_message` unconstrained while an incompatible-at-construction 0.1.0 is coming. Against real smart_message `develop` (0.1.0, Lanet stand-in because of F-1): publish, dispatcher key (#9 holds), inbound `receive` and heartbeat work; construction still fails (F-2); #29 constructs with `logger:` but not without it; #29 plus F-51's `SmartMessage::Logger.default` constructs both ways. Once 0.1.0 ships, Ruby ≥ 4 resolves 0.1.0 and 3.3/3.4 resolve 0.0.17. Proposed: constrain `~> 0.1` and floor 4.0 when 0.1.0 ships, folded into F-24/F-10. status: **needs Dewayne's decision** (inbox).
- **F-56:** `publish` reports success when every send fails. `publish_to_nodes` (`lanet.rb:283-302`) rescues per node and never raises; smart_message 0.1.0's transport contract says `do_publish` must raise on failure. Probe: 5 publishes to one always-failing node, all `success? == true`, circuit stays `:closed`. Tests cover zero-node and partial cases only. Fix: raise `Error` when `target_nodes` is non-empty and `published_count` is 0, plus one test. status: **issue [#33](https://github.com/MadBomber/smart_message-transport-lanet/issues/33)**; build after #23/#25–#29 merge.
- **F-15 (update):** baseline exits 0 on develop and on each of #20–#29's heads individually. status: **PR open [#32](https://github.com/MadBomber/smart_message-transport-lanet/pull/32)**.
- **F-21 (update):** `Gemfile` `minitest ~> 6.0` + `gem "minitest-mock"`, then `bundle lock --conservative --update minitest`; lock diff is minitest 6.1.0, minitest-mock 5.27.0, DEPENDENCIES; 64/307 green on 4.0.9; `git merge-tree` clean against each of #20–#29 including #22. A major dev-dependency bump that edits `Gemfile.lock`, so still after #22 merges.
- **F-18 (update):** on 0.1.0 `header[:capabilities] =` still raises; the header `extensions` bag carries it when set at construction, so the code-side fix becomes `header.dig("extensions", "capabilities")` once 0.1.0 is targeted.
- **F-4 (note):** stub Base sets `SmartMessage::VERSION = "0.1.0"` but models 0.0.17. A suite against real 0.1.0 must set `SMART_MESSAGE_DLQ_PATH` to a temp file, or it writes to the developer's real DLQ.
- S-2/S-4/S-7: re-probed against 0.1.0; details held locally.
