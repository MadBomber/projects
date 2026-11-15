# Findings — smart_message-transport-named_pipes

Audit backlog for this repo. Numbered findings persist; statuses update as they become
issues (`issue #N`), PRs, or are resolved. Security details are held locally in
`notes/security/smart_message-transport-named_pipes.md`; only placeholders appear here.

## Audits table

| audit | last run | commit | result |
|-------|----------|--------|--------|
| security (+dead-code) | 2026-11-15 | 91197bb | bundle-audit clean (scratch lock, smart_message 0.0.17); S-9 new (local); S-7 resolved; S-1–S-4, S-6, S-8 still hold (S-3 narrowed); F-35 resolved; no new F-findings from the audit; improver F-92 → #59, F-93 → #60, F-94 → #61; dead-code delta 6725e5c..91197bb: none new (F-2/F-3 hold) |
| quality | 2026-11-15 | 91197bb | RuboCop 124→131 in 10 files (88 autocorrectable); 52 runs, 1 failure (F-84 → PR #57), 0 skips; -w clean on 4.0.9 and 4.1p1; flog 359.4 (start_pipe_reader 99.5); coverage 136/155 lines, 29/34 branches; F-96/F-97 need Dewayne (framing); F-98 → #62, F-99 → #63, F-100 → #64; S-10 (local); F-41/F-44/F-45 resolved; F-39/F-40/F-42/F-43 hold |
| dependencies | 2026-11-15 | 91197bb | vs smart_message 0.1.0 (GitHub develop 2bf06e8, +24 since 2aa3dbf; still untagged, not on RubyGems, Ruby ≥ 4.0): suite 51/52 vs real gem (only F-50); framework e2e OK on 4.0.9 and 4.1.0-preview1 runtime-only, -w clean; bundle-audit clean; outdated: minitest (capped, F-20), hashie 6.0 (upstream pin, F-101), redis-client (transitive); F-51 grown; F-14 subscribe half unverified; F-46/F-47/F-52/F-53/F-62 hold; F-102 toolchain note; improver F-103 → #65, F-104 → #66 |
| adoption (+dead-code delta) | 2026-11-15 | 91197bb | vs real smart_message 0.1.0 (GitHub develop 2aa3dbf), Ruby 4.1.0-preview1: F-84 develop CI red since #52 (signal-test child lacks -Itest) → PR #57, F-85 README Running Tests lists 3/5 files → PR #58, F-86 transport takes over host INT/TERM + exit 0 (Dewayne), F-87 require-time ENV fallback in DEFAULT_CONFIG → #56; F-14/F-24/F-30/F-31/F-47/F-53 hold; F-8/16/27/34/36/45/48/73 resolved; no mkdocs/llms.txt; gem-skill blocked by #6; dead-code delta c143600..91197bb: none new |

## 2026-11-10 — madbot — security + dead-code (first visit) — develop @ 8d0d2af

Dead-code scan reached develop @ 8d0d2af.

- **S-1:** security finding, details held locally. status: open (local-only; TOCTOU class).
- **S-2:** security finding, details held locally. status: open (local-only).
- **S-3:** security finding, details held locally. status: open (local-only).
- **S-4:** security finding, details held locally. status: open (local-only; also surfaced as a
  neutral design note in inbox.md — host signal-handler hijack).
- **S-5:** security finding, details held locally. status: **PR open (neutral packaging fix this
  visit)** — ship fix excludes the test stub from the gem.
- **S-6:** security finding, details held locally. status: unverified (theoretical).

- **F-1:** `publisher_pipe_name` is dead code — zero call sites in lib (transport writes to the
  subscriber pipe via `do_publish`). status: **PR open #8** (removes only the private method + its
  dedicated test; the public `attr_reader :publisher_pipes` = F-2 is kept, Dewayne's call).
- **F-2:** `@publisher_pipes` / `attr_reader :publisher_pipes` is vestigial — initialized and
  cleared, never populated or read. The `attr_reader` is **public API**, so removal needs a
  deprecation decision. status: **needs Dewayne's decision** (see inbox.md).
- **F-3:** `:buffer_size` and `:mode` config options are documented (DEFAULT_CONFIG + YARD +
  README) but never read anywhere in lib — inert, misleading knobs. status: **needs Dewayne's
  decision** (implement vs remove; both are documented public options). See inbox.md.
- **F-4:** duplicate `VERSION` constant — defined inline in named_pipes.rb and again in
  named_pipes/version.rb (never required); gemspec hardcodes a third copy. status: **PR open**
  (lib-only single-source via version.rb this visit).

### Other verified items (not security, not yet issues)

- **F-5:** gemspec requires `smart_message >= 0.1.0`, but RubyGems only publishes `smart_message`
  up to 0.0.17, so `bundle install`/`gem install` cannot resolve — the gem is uninstallable as
  released. Lowering the floor to `>= 0.0.17` makes `bundle install` succeed (verified), but the
  correct floor is a release decision (lower floor vs. publish smart_message 0.1.0 first). status:
  **issue #6** + inbox.md.
- **F-6:** no `.github/` CI — nothing runs the test suite or lint on any branch. status:
  **issue #7** (CI must run tests directly via `ruby -Itest -Ilib` since bundle can't resolve, and
  must not wire rubocop yet — ~137 pre-existing offenses).
- **F-7:** README "Zero External Dependencies / Uses only Ruby standard library" sits beside a
  gemspec `add_dependency "smart_message"`. status: **PR open #10** (docs-only wording fix).

## 2026-11-10 (visit 2) — madbot — quality audit — develop @ 2a49fcc

RuboCop 1.92.2 baseline: 137 offenses in 8 files (no gate; ties to F-6/#7). Tests: 45 runs,
0 failures, 9 skips (7 error-path, 2 integration, most "Requires full SmartMessage framework").

- **F-8:** signal handler (`setup_signal_handlers`, named_pipes.rb:354-363) calls `notify_shutdown`
  + `cleanup_pipes`, both using `@mutex.synchronize`, which raises `ThreadError: can't be called
  from trap context` — graceful shutdown on INT/TERM is completely broken and pipes leak. Also a
  library constructor hijacks the host app's INT/TERM handlers. Reproduced. status: **issue #12**.
  (Same code as security S-4; #12 is neutral/correctness-only.)
- **F-9:** `lib/smart_message/transport/base.rb` stub (self-described "will be replaced by the
  actual smart_message gem", empty no-ops) ships in the gem and is `require`d from lib, so on an
  installed consumer the require can resolve to the no-op stub instead of the real Base
  (non-deterministic $LOAD_PATH order). status: open — **partly addressed by PR #4** (excludes the
  stub from the packaged gem). Deeper fix (move stub to test/support, drop the lib require) is a
  bigger change; noted in inbox.
- **F-10:** newline framing — writes use `pipe.puts(data)` (named_pipes.rb:294), reads use
  `pipe.gets` + `data.strip` (:245-248), so any payload with an embedded newline is truncated at
  the first `\n` and `strip` mutates whitespace-bearing payloads. status: **needs Dewayne's
  decision** (wire-format change; implement length-prefix/escaping vs document the single-line
  constraint). See inbox.
- **F-11:** both `examples/*.rb` use nested `def` (Lint/NestedMethodDefinition at
  basic_pubsub.rb:41, multiple_processes.rb:53/74/96) — a Ruby antipattern in shipped examples.
  status: **issue #11**.
- **F-12:** `.envrc` (`export RR=\`pwd\``) and `Rakefile` are packaged into the released gem — the
  gemspec `reject` block omits them. Dev-only noise in a published gem. status: open — gemspec
  change, overlaps PR #4's `spec.files` area (stack or fold into a future packaging PR; not an
  independent candidate while #4 is open).
- **F-13:** unit tests build the transport via `NamedPipes.allocate` + hand-set ivars, so the real
  `initialize`/signal/`at_exit` path is never exercised (why F-8 went unnoticed); 9 skips, several
  of which the vendored stub Base could satisfy. status: open (judgment-heavy; not an obvious-yes
  PR). Pairs with F-8.

## 2026-11-10 (visit 3) — bomberbot — dependencies audit — develop @ 2a49fcc

Checked against the real smart_message 0.0.17 (scratch bundle, Ruby 4.0.9) with the stub
`transport/base.rb` off the load path. Driven through the framework (`Msg.subscribe(proc)` +
`msg.publish`) the transport works end to end. Suite 45 runs / 0F / 9 skips against both stub
and real gem, but only because the tests avoid the paths below (F-13). Dead-code scan not rerun;
last commit stays 2a49fcc.

- **F-14:** README, examples, and YARD teach a transport-level API the real smart_message 0.0.17
  doesn't support: `transport.publish(MyMessage, msg)` (README.md:66,133; examples; YARD
  named_pipes.rb:22) raises `ArgumentError` (real `Base#publish(message)` has arity 1), and
  `transport.subscribe(MyMessage, method(:h))` registers but every delivery fails in the real
  dispatcher (`undefined method 'split' for an instance of Method`). status: **needs Dewayne's
  decision** (rewrite docs to the framework API vs add transport-side adapters; taste says change
  code, but the contract is upstream Base). verified.
- **F-15:** `unsubscribe` looks up `@dispatcher.subscribers[message_class]` with a Class, but the
  real dispatcher keys by String, so unsubscribing one of two handlers removes the pipe and reader
  while the other is still subscribed (and inserts a phantom Class key). status: **issue #13**.
  verified. Obvious-yes PR when the queue clears.
- **F-16:** no `unsubscribe!` override — `Msg.unsubscribe!` clears the dispatcher but leaves the
  FIFO and reader thread. status: **issue #15**. verified.
- **F-17:** the stub Base diverges from the real 0.0.17 contract (publish arity, no-op subscribe,
  any-key dispatcher, public receive/logger, missing serializer/unsubscribe!/circuit breakers);
  F-14–F-16 all hide behind it. status: open (judgment-heavy; pairs with F-9/F-13). verified.
- **F-18:** even with F-5's floor lowered to `>= 0.0.17`, a fresh resolve can't load smart_message:
  `require 'ap'` with amazing_print only a dev dep; amazing_print 3.0 breaks on ActiveSupport
  `try`; lumberjack 2.1 breaks the logger. Works only with `amazing_print ~> 1.6` + `lumberjack
  ~> 1.2` pinned by the consumer. Settles F-5: lowering the floor alone ships a gem that installs
  then crashes. status: **needs Dewayne's decision** (cross-repo release ordering). verified.
- **F-19:** Ruby floor `>= 3.2.0` (gemspec:13, .rubocop.yml) is EOL since 2026-04-01; taste
  (debug_me#17) says raise it. status: **needs Dewayne's decision** on `>= 3.3` vs `>= 4.0`;
  gemspec, so sequence after #4. verified.
- **F-20:** minitest capped `~> 5.16` in both Gemfile:11 and gemspec:39; suite passes unchanged on
  minitest 6.1.0 (45/0F/9S). Dev deps also declared twice (Gemfile + gemspec). status: open — can't
  be proved on `develop` until #6 lets `bundle install` resolve. verified.
- **F-21:** the two `rescue Errno::ENXIO` branches (reader :253, writer :304-306) are unreachable —
  the FIFOs are opened in blocking mode, so `open` blocks rather than raising ENXIO — and the
  comment "Open pipe in non-blocking mode" (:240) doesn't match the code. Further detail held
  locally. status: open. verified.
- **F-22:** (triage + improver) `unsubscribe`/`disconnect` stall ~2s per idle channel and then
  `Thread#kill` the reader, because the reader blocks in `File.open(pipe, 'r')` and never sees the
  stop flag (1 channel 2.14s, 3 channels 4.01s). status: **issue #14**. verified.
- **F-23:** (improver) security-adjacent change to FIFO creation; details held locally with S-3.
  status: open (local).
- F-10 update: with the real default JSON serializer, embedded newlines are escaped, so `"a\nb"`
  round-trips; F-10 bites only custom serializers that emit raw newlines.
- Ruby 4 readiness: lib clean under 4.1.0-preview1 `-W:deprecated -W`; only 3 unused-variable
  warnings in tests.

## 2026-11-12 (visit 4) — madbot — adoption audit — develop @ b2042fc

First adoption audit. Run against the real smart_message 0.0.17 (Ruby 4.0.9, scratch gems,
lumberjack pinned ~> 1.2 per F-18) with the stub base.rb off the load path. No mkdocs.yml; no
llms.txt; gem-skill not run (gem uninstallable/unpublished). No new security findings.

- **F-24:** README "Complete Example" and both `examples/*.rb` define messages with
  `attribute :x, :type`, which neither real smart_message 0.0.17 nor the stub provides — they die
  at class definition (`undefined method 'attribute'`). Real DSL is `property`, and `from` is
  required. Verified working replacement: `from`, `property`, `config { transport T }`,
  `def self.process`, `Msg.subscribe`, `Msg.new(...).publish`. Examples' `require 'bundler/setup'`
  also fails (#6). status: **needs Dewayne's decision** (tied to F-14: framework path vs
  transport-side API). verified.
- **F-25:** README "Related Projects" lists `smart_message-transport-tcp`, `-redis`, `-http`; none
  exists (RubyGems 404, no repos). Real siblings: -lanet, -email, -rabbitmq, -nats; Redis/memory/
  stdout/file are built into smart_message. status: **issue #19**. verified.
- **F-26:** gem never released (no tags, RubyGems 404) but CHANGELOG has dated `[0.0.1] -
  2025-11-13` and README says `gem install …`. status: **needs Dewayne's decision** (release;
  blocked by #6/F-18). verified.
- **F-27:** silent message loss under bursty publish: `write_to_pipe` rescues `Errno::EPIPE` and
  reports success, and the reader closes/reopens the FIFO after every EOF, so a writer racing the
  reopen loses its message. 5000 in-process publishes received 4986–4998; cross-process 2998/3000.
  Fix: reader holds FIFO open `r+` (or dummy writer), or retry on EPIPE. Interacts with #14.
  status: **issue #18**. verified (intermittent).
- **F-28:** gemspec metadata thin: no `bug_tracker_uri`, `documentation_uri`,
  `rubygems_mfa_required`; `changelog_uri` mis-indented. GitHub repo has no description/topics
  (Dewayne-only settings). status: open, verified; small gemspec PR (never self-merged).
- **F-29:** no `llms.txt`. status: open; sequence after F-14/F-24.
- **F-30:** README claims "hundreds of thousands of messages per second"; measured ≈19k/s raw,
  ≈2.7k/s through the framework (open/close per publish). status: **needs Dewayne's decision**
  (fix docs vs code). verified.
- **F-31:** README troubleshooting "subscribe AFTER connect" and `configure` YARD ("called by
  #connect") are wrong for the real gem: real `Base#initialize` calls `configure`. status: open,
  verified; fold into F-24 rewrite.
- **F-32:** CHANGELOG `[Unreleased]` omits merged #10 (README dependency-claim fix). One line
  under Fixed. status: open, verified; low-risk docs PR next visit.
- **F-33:** (improver) `connect` after `disconnect` returns a dead transport: `disconnect` sets
  `@shutdown = true` and removes the pipe directory; `connect` never clears the flag and skips
  `configure` because `@pipe_path` is set → `connected?` false, subscribe raises
  `Failed to create named pipe`. Fix: `@shutdown = false` + always `configure` in `connect`.
  status: **issue #17**. verified.
- Updates: F-12 now also ships `.github/workflows/ci.yml` (and `.envrc`, `Rakefile`) in the built
  gem — still a one-line gemspec reject-list fix. F-4 residual: gemspec:5 still hardcodes
  `"0.0.1"`; requiring version.rb first raises `superclass mismatch` (needs Dewayne: regex-read vs
  separate constant). F-8: README advises SIGTERM/SIGINT for graceful shutdown — true only after
  #12. Issue #7 resolved on develop by #9 (BomberBot commented; closing is Dewayne's).
- Dead-code scan: no new dead code at b2042fc (F-1 removed by #8).
- Status updates (visit 4): F-11/#11 → merged low-risk [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16) at 6725e5c; F-15/#13 → PR [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20); F-16/#15 → PR [#21](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/21) (stacked on #20).

## 2026-11-12 (visit 5) — madbot — security + dead-code — develop @ 6725e5c

Diff since 8d0d2af reviewed (lib: #5 version require, #8 helper removal; gemspec: #4; new ci.yml
from #9). `bundle-audit check` (scratch lock, smart_message floor lowered to 0.0.17): no
vulnerabilities. **Dead-code scan reached develop @ 6725e5c**: debride flags only public
API/framework hooks plus `publisher_pipes` (F-2); no new dead code.

Status updates this visit: **F-25 / #19** → PR open [#22](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/22). **F-33 / #17** → PR open
[#27](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/27). F-12 and F-32 still hold (F-32 waits until the CHANGELOG-touching PRs land).

- **S-7:** security finding, details held locally. status: open (local-only).
- **F-34:** a `receive` that raises makes the reader drop the other messages read in the same
  buffer and sleep ~1s before reopening (catch-all `rescue` outside the `File.open` block in
  `start_pipe_reader`). Probe: `"bad\ngood1\ngood2"` → received `["bad"]`; next write returned at
  ~1.1s. Fix: rescue per message inside the loop + test. Same loop as #14/#18.
  status: **issue [#23](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/23)**.
- **F-35:** `.github/workflows/ci.yml` has no `permissions:` block (default token scope). Fix: add
  `permissions: contents: read`. Push needs the `workflow` scope or SSH. status: **issue [#25](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/25)**.
- **F-36:** (improver) `cleanup: false` makes `disconnect` skip stopping the reader threads too
  (`cleanup_pipes` returns before `stop_pipe_reader`): `live readers=1` after disconnect.
  status: **issue [#24](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/24)**. (#27 makes `subscribe` restart a dead reader after
  reconnect but doesn't stop live ones.)
- **F-37:** (improver) `test_cleanup_pipes_respects_cleanup_option` ended in `assert true`;
  `test_write_after_disconnect` never asserted; three unused `pipe_name` warnings under `-w`.
  status: **merged (low risk) [#26](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/26)** at 52f8e45.
- **F-38:** (improver) unreachable `Errno::ENXIO` rescues (named_pipes.rb reader/writer; both opens
  are blocking) and the wrong "non-blocking mode" comment — extends F-21. status: open; hold until
  the #14 reader rework decides whether a nonblocking open returns.

Re-confirmation at 6725e5c: S-1–S-4, S-6 still present; S-5 resolved by #4 (4470127); F-1
resolved by #8; F-2, F-3 hold (needs Dewayne's decision); F-4 lib part resolved by #5, gemspec:5
still hardcodes `"0.0.1"` (requiring version.rb first → superclass mismatch); F-12 holds.
F-8 note: every `NamedPipes.new` adds an `at_exit` hook holding the instance and re-sets the
traps; folds into the #12 fix.

## 2026-11-12 (visit 6) — bomberbot — quality audit — develop @ 52f8e45

RuboCop 1.92.2: 124 offenses / 8 files (was 137 at 2a49fcc: −4 NestedMethodDefinition and −3
MethodLength/AbcSize via #16, −3 UselessAssignment via #26). Tests 44 runs / 0F / 9 skips; `ruby -w`
clean (was 3 warnings). flog 306.9 total, `start_pipe_reader` 92.9. reek 37 warnings. flay 358.
Clean probes (not findings): reader latency with a held-open writer (0.00s); concurrent `subscribe`
made no duplicate readers in 30 trials. Dead-code scan not re-run (develop unchanged since 52f8e45).

- **F-39:** two subscribers to one class share a FIFO; only its creator tracks it, so the creator's
  `disconnect` unlinks it (`cleanup_pipes` :320-323) and the survivor's reader loops ENOENT→sleep
  while publishers drop every message silently. A leftover FIFO from a crash is never cleaned.
  Probe `two_subs.rb`: after A disconnects, B gets 0 of 10 new messages, publish errors=0.
  Ownership semantics depend on F-40. status: **issue [#29](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/29)**.
- **F-40:** one FIFO per channel makes multiple subscribers *compete* (20 publishes split 13/7)
  rather than each getting every message, though README says pub/sub and examples/README.md:84
  suggests multiple subscribers per type. Fan-out per subscriber, or document as a work queue?
  status: **needs Dewayne's decision** (inbox).
- **F-41:** `test_timeout_on_write_to_blocked_pipe` (errors_test:50-68) and
  `test_pipe_timeout_when_no_reader` (integration_test:96-113) are the same test (each waits a real
  timeout); setup/teardown copied across all three test files. Fix: drop the integration copy,
  share setup in test_helper. status: open, verified — test-only PR once #20/#21/#27 land.
- **F-42:** RuboCop runs in the default `rake` task with no baseline: 124 offenses (82
  safe-autocorrectable; 26 `Style/StringLiterals`). `.rubocop.yml` lacks `NewCops:`; examples have
  shebangs without the exec bit. status: open, verified; autocorrect one area per PR after the lib PRs land.
- **F-43:** the whole read/dispatch/rescue loop is inline in `start_pipe_reader`'s `Thread.new`
  block (flog 92.9), untestable in isolation (cause of 2 skips). Extract `deliver_lines(io, klass)`
  as part of the #23 fix. status: open; fold into #23/#14/#18.
- **F-44:** CI matrix is 3.2/3.3/3.4 only, no Ruby 4.0; suite passes on 4.0.9 and 4.1.0-preview1.
  status: **issue [#30](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/30)** (same file as #25).
- **F-45:** (improver) `DEFAULT_CONFIG` reads `SMART_MESSAGE_PIPE_BASE`/`SMART_MESSAGE_NAMESPACE`
  at require time; set later they're ignored (verified: `base_path` stays `/tmp/smart_message/pipes`).
  The two env tests only assert non-empty strings. Fix: `default_options` merges ENV at call time.
  status: **issue [#28](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/28)**.

Re-confirmed at 52f8e45: F-2, F-3, F-10, F-12, F-13, F-21/F-38 hold; F-8 unchanged. Also
(improver) gemspec `spec.bindir = "exe"`/`spec.executables` are dead (no `exe/`) — fold into an F-12
packaging PR.

## 2026-11-13 (visit 7) — madbot — dependencies audit (+dead-code delta) — develop @ c143600

Checked against smart_message 0.1.0 from GitHub `develop` @ 2aa3dbf (stub `base.rb` off the load path, Ruby 4.0.9, minitest 6.1.0). Through the framework (`Msg.subscribe` + `msg.publish`) the transport works end to end on 0.1.0. bundle-audit clean. `bundle outdated`: only minitest (5.27.0 → 6.1.0, capped). Dead-code scan reached c143600: nothing new (new private `subscribers?` is used by `unsubscribe`).

- **F-46:** smart_message 0.1.0 exists only on GitHub (no tag, RubyGems max 0.0.17). Against it a fresh resolve succeeds, so **F-18 is resolved on 0.1.0** and the `>= 0.1.0` floor is right; #6 now needs only the upstream release. status: **needs Dewayne's decision** (release order); noted on #6.
- **F-47:** smart_message 0.1.0 requires Ruby `>= 4.0`; this gem declares `>= 3.2.0` (gemspec:13), README:37, `.rubocop.yml` TargetRubyVersion 3.2, CI 3.2–3.4. Settles F-19 at `>= 4.0`. status: **needs Dewayne's decision**; PR-ready once confirmed (gemspec, README, .rubocop.yml).
- **F-48:** `named_pipes.rb:3` requires only `smart_message/transport/base`; under 0.1.0 (Zeitwerk) `NamedPipes.new` raises `NameError: uninitialized constant SmartMessage::Dispatcher` unless the caller required `smart_message` first; bin/console:5 too. status: **issue #41**.
- **F-49:** the test stub reopens the real Base when both are on the load path (stub `initialize`/`publish`/`receive`/`subscribe` win, -W "method redefined"). Fix: drop the stub and test against 0.1.0, or move it under `test/support/` with a guard. status: open; verified (base.rb in flight in #34).
- **F-50:** with the stub removed and `-rsmart_message`, the suite passes against real 0.1.0 except `test_subscribers_predicate_treats_missing_or_empty_as_none` (named_pipes_test.rb:248, builds stub-only `Base::Dispatcher`). Fold into F-49. status: open; verified.
- **F-51:** `SmartMessage::Transport.create(:named_pipes)` → `ArgumentError: Unknown transport` on 0.1.0; manual `register` works. status: **needs Dewayne's decision** (new public entry point).
- **F-52:** 0.1.0's contract says `do_publish` must raise on failure; it returns quietly with no subscriber pipe (named_pipes.rb:112-115) and swallows EPIPE/ENXIO (:302-310), so `PublishResult#success?` is true and nothing is DLQ'd. status: **needs Dewayne's decision** (is no-subscriber a success?).
- **F-53:** 0.1.0's `Base#initialize` calls `configure`, so `connected?` is true before `connect`; YARD at named_pipes.rb:86 incomplete. status: open; verified (named_pipes.rb in flight).
- **F-54:** gemspec:5 hard-codes `spec.version = "0.0.1"` (F-4 residual); `require_relative` of version.rb raises `superclass mismatch` (version.rb reopens `class NamedPipes` without superclass). status: **PR open #39** (reads VERSION as text).
- **S-8:** security finding, details held locally. status: open (local-only).
- Re-verified: F-14 holds on 0.1.0 (`publish` arity error; `subscribe(method)` NameError in `resolve_handler`); F-15 fixed by #20; F-16 holds until #34 (#21 never reached develop); F-20 holds (minitest 6.1 passes the stub suite); F-24 holds on 0.1.0.

### Improver (visit 7)

- **F-60:** gemspec packages `.envrc`, `.github/`, `Rakefile`; `bindir`/`executables` dead (no `exe/`); missing `bug_tracker_uri`/`documentation_uri`/`rubygems_mfa_required` (Dewayne's lanet#21 pattern). status: packaging + dead lines **PR open #39** (with F-12); metadata left for after lanet#21 lands (inbox).
- **F-61:** README "How It Works" never says publish with no subscriber drops the message (debug log only). status: **merged (low risk) #43** at 4a27bc3.
- **F-62:** `create_subscriber_pipe` ignores `system("mkfifo", …)`'s return; a permission failure surfaces as `ENOENT ... apply2files`, while README Troubleshooting promises `Errno::EACCES`. status: open; verified; overlaps a local security item, so fix with `File.mkfifo` alongside it, not filed publicly yet.
- **F-63:** #21 was merged into stale base `madbot/unsubscribe-key`, never develop; #34 redoes it. status: process note (taste).

### Triage (visit 7)

- **F-70:** `test_write_to_nonexistent_pipe` wrote to real `/tmp/nonexistent_pipe_12345.pipe` with zero assertions. status: **merged (low risk) #44** at b162732 (path moved under @test_dir; still assertion-free until #40).
- **F-71:** two tests skipped as "requires full framework" pass without it. status: **merged (low risk) #44**.
- **F-72:** errors/integration teardown disconnected nothing (`@transport` never set). status: **merged (low risk) #44**.
- **F-73:** `write_to_pipe` opens with `'w'`, so a FIFO removed after the `File.exist?` check becomes a regular file and `create_subscriber_pipe` then skips mkfifo. status: **issue #40**.
- **F-74:** gemspec RuboCop offenses (`changelog_uri` indent, dev-dependency order). status: open; trivial, rides a future gemspec PR.
- **F-41** (re-verified, integration copy 2.15 s): status: **issue #42**.

### Responder (visit 7)

- **F-80:** #12 still reproduces on c143600 (SIGTERM → ThreadError, exit 1); the issue's "pipes leak" is wrong for a normal exit (at_exit cleans up). Commented on #12.
- **F-81:** Dewayne's #37 is red only on Ruby 3.2 (`Expected 1.0005… to be < 1.0`); likely the `rescue SystemCallError` `sleep(1)` after `Thread#wakeup` interrupts the blocking open. Unconfirmed (no Linux runner). Inbox review note.
- **F-82:** pairwise merges of #31–#38: CHANGELOG conflicts among #33/#35/#36/#37/#38 (all add at top of Fixed); test-file conflicts #35+#37, #36+#38; `named_pipes.rb` conflict #37+#38. #31/#32/#34 clean. Inbox review note.
- **F-83:** #29 still reproduces with #35's and #38's heads; no open PR touches it.

## 2026-11-15 (visit 9) — bomberbot — adoption audit (+dead-code delta) — develop @ 91197bb

Run against real smart_message 0.1.0 (GitHub develop 2aa3dbf), Ruby 4.1.0-preview1, plus a packaged-gem simulation (stub base.rb removed). No mkdocs.yml, no llms.txt; gem-skill still blocked by #6. Dead-code scan reached 91197bb: debride flags only public API/framework hooks + F-2; new private `release_channel`/`handle_shutdown_signal` are called; nothing new dead.

- **F-84:** `develop` CI red since #52 (b76f162; runs 43529907474, 43529936727; #51 green). `named_pipes_signal_test.rb:56` spawns its child with `-I LIB_DIR` only; since #52 lib does `require 'smart_message'`, which resolves to the `test/smart_message.rb` shim only with `-Itest`, so the child dies → "child should have created its FIFO" (:59). Fix: add the test dir to the child's `-I`. status: **PR open #57**. Source: CI (tender, triage, auditor and improver all found it).
- **F-85:** README "Running Tests" lists 3 of 5 test files (omits require and signal tests), hedges `rake test (if available)`. status: **PR open #58**.
- **F-86:** `NamedPipes.new` replaces the host app's INT/TERM handlers and, since #50, exits 0 on either signal (host trap set before `new` never ran; status 0 not 143). README doesn't say the transport takes over process signals. status: **needs Dewayne's decision** (document vs opt-out such as `trap_signals: false`; also one `at_exit` per instance). Relates to F-8.
- **F-87:** `DEFAULT_CONFIG` (named_pipes.rb:39-40) still reads the pipe env vars at require; `default_options` falls back to them, so a var set at require and unset later still applies (`SMART_MESSAGE_NAMESPACE=early`, require, `ENV.delete` → `"early"`). status: **issue #56** (after #37 lands).
- **F-32 update:** CHANGELOG `[Unreleased]` also omits #39 (packaging) and #48 (built-in transports docs), besides #10. Hold until #37 lands (CHANGELOG conflict).
- **F-88:** (improver) the lib `transport/base.rb` stub could move to `test/` now that `test/smart_message.rb` is the entry shim (drops the gemspec reject line); trial green with F-84 applied. status: open; after the F-84 PR merges; folds into F-49.
- **F-89:** (improver) two tests now hand-spell `-I test -I lib` for child processes (signal, require); a test-helper method for spawning children would stop F-84 recurring. status: open; small test-only follow-up.
- **F-53** re-verified (`connected?` true right after `new`, because `configure` creates the directory); not filed — arguably intended. status: **needs Dewayne's decision** (should `connected?` mean "connect was called"?).
- **F-21/F-38** re-verified by probe: blocking `File::WRONLY` open blocks until Timeout, reader opens `r+` immediately, so both ENXIO rescues are unreachable. status: open; hold until #37 lands (same file); not filed publicly (F-21 has local detail).
- **#29 / F-39** re-verified on 91197bb (A's disconnect deletes the shared FIFO; B gets 0/10 with no error, since its `r+` reader keeps the unlinked FIFO open). The issue's "ENOENT loop" wording is stale. Self-heal is possible but throwaway if F-40 picks fan-out. status: issue #29, waits on F-40.
- **Resolved** at 91197bb: F-8/#12 (#50), F-16/#15 (#34), F-27/#18 (#38), F-34/#23 (#36), F-36/#24 (#35), F-45/#28 (#33; residue F-87), F-48/#41 (#52), F-73/#40 (#51), F-35/#25 (#31), F-44/#30 (#32; issue open, Dewayne closes), F-41/#42 (#47), F-12/F-54/F-60 packaging (#39), F-25/#19 (#22/#48). F-22/#14: disconnect prompt since #38; unsubscribe still 2.1 s until #37.
- **F-74 / F-60 metadata:** gemspec RuboCop + missing `bug_tracker_uri`/`documentation_uri`/`rubygems_mfa_required` (lanet#21 pattern, merged 2026-11-13). status: **PR open #55**.
- **F-90:** (builder, #57 review) `run_child_and_signal` uses block-form `popen3`, which waits for the child on exit; if an assertion or Timeout fires while the child is alive, nothing sends KILL and CI hangs; stderr is read only after exit. Pre-existing from #50. status: open; small test-only follow-up after #57.
- **F-91:** (builder, #58) CI's per-file loop stops at the first failing file under `bash -e` (so `named_pipes_test.rb` never ran while the signal test was red) and globs only `test/smart_message/transport/` while the Rakefile uses `test/**`. README Development still says "run `bundle install`", which fails until #6. status: open; workflow part → Dewayne (inbox), README line after #6.

## 2026-11-15 (visit 10) — bomberbot — security audit (+dead-code delta) — develop @ 91197bb

Diff 6725e5c..91197bb reviewed (lib: #33 #34 #35 #36 #38 #50 #51 #52; gemspec #39; ci.yml #31 #32).
`bundle-audit check` (scratch lock, smart_message pinned 0.0.17 because of #6): no vulnerabilities.
No eval, no YAML/Marshal loads; ENV read only in `DEFAULT_CONFIG`/`default_options` (F-87).
**Dead-code scan reached develop @ 91197bb.** New private helpers (`subscribers?`, `release_channel`,
`handle_shutdown_signal`) all have call sites; nothing new dead. F-2 / F-3 hold (needs Dewayne's decision).

- **F-35:** resolved — ci.yml has `permissions: contents: read`.
- **S-1:** security finding, details held locally. status: open (local-only).
- **S-2:** security finding, details held locally. status: open (local-only).
- **S-3:** security finding, details held locally. status: open (local-only; narrowed).
- **S-4:** security finding, details held locally. status: open (local-only).
- **S-6:** security finding, details held locally. status: unverified.
- **S-7:** security finding, details held locally. status: resolved.
- **S-8:** security finding, details held locally. status: open (local-only; mainly upstream).
- **S-9:** security finding, details held locally. status: open (local-only; verified; neutral fix after #37 lands).
- Pre-existing FIFO at the subscriber path is reused but not tracked, so `unsubscribe!`/`disconnect` leave it — the "only the creator tracks it" half of F-39 / #29; waits on F-40.

Improver (critic read, same commit):

- **F-92:** `test_shutdown_flag_thread_visibility` exercises no transport code and `test_special_characters_in_channel_name` asserts a `File.join` tautology (errors_test.rb:162-179, 205-223) — removal, test-only (~−37). status: issue [#59](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/59).
- **F-93:** test stub `Logger` (lib/.../transport/base.rb:76-81) never yields, so ~30 `logger.x { "…" }` blocks in named_pipes.rb are never evaluated by the suite (probe: ~4,200 block evals, none raise today) — guard. Pairs with F-88. status: issue [#60](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/60).
- **F-94:** `derive_channel_name` collides (`Billing::Event`/`Billing_Event`, `Order`/`ORDER`) → one pipe, second class's handlers silently never fire (probe-verified). Non-breaking first step: raise in `subscribe` on a channel bound to another class; naming-scheme change → Dewayne. status: issue [#61](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/61).
- **F-95:** `test_that_it_has_a_version_number` duplicated by `test_version_format` (named_pipes_test.rb:23-29) — trivial, same file as #37; fold into a later test PR. status: open.

## 2026-11-15 (visit 10b) — madbot — quality audit — develop @ 91197bb

RuboCop 1.92.2: 131 offenses in 10 files (was 124 in 8), 88 safe-autocorrectable. Suite run per file (`ruby -w -Itest -Ilib`): 52 runs, 1 failure (F-84, fixed by PR #57), 0 skips (was 9); `-w` clean on 4.0.9 and 4.1.0-preview1. flog 359.4 (was 306.9; `start_pipe_reader` 99.5). reek: 46 warnings. Coverage of named_pipes.rb: 136/155 lines, 29/34 branches. Dead-code scan not re-run: the visit-10 scan already reached 91197bb.

- **F-96:** Concurrent publishers corrupt each other's messages once a message exceeds PIPE_BUF (512 B on macOS, 4096 on Linux). Every publish still reports success. Probe: 4 publishers × 200 messages of 16 KB → 588/800 received, 511 corrupt; at 8 KB everything was clean. Fix: a writer lock or length-prefixed framing, which is a wire-format choice tied to F-10/F-40. status: **needs Dewayne's decision** (inbox); verified.
- **F-97:** A publish that times out partway through `puts` leaves partial bytes in the FIFO. The next, successful publish is appended to them, so it is corrupted and lost. Probe: a 300 KB publish with timeout 0.3 raised, and the following "NEXT" message arrived glued to the tail of the partial write. Fix: framing (same decision as F-96/F-10), or no timeout once the first byte is written. status: **needs Dewayne's decision** (inbox); verified.
- **F-98:** `cleanup_pipes` runs `Dir.rmdir(@pipe_path)` on the shared namespace directory, including from `at_exit`. Every other transport on that path then loses it: `connected?` returns false and `subscribe` raises ENOENT. `test_multiple_transports_same_namespace` asserts nothing after its disconnects. Fix: `mkdir_p` in `create_subscriber_pipe` and drop the rmdir, plus a regression test. Lib change in the same file as #37. status: issue [#62](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/62).
- **F-99:** The publish error paths have no test: the `do_publish` rescue/raise (:128-130) and the `write_to_pipe` SystemCallError wrap (:348-350). Both are reachable (no-reader timeout; a 0400 FIFO gives EACCES). Fix: two tests in errors_test. Test-only, low-risk eligible. status: issue [#63](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/63).
- **F-100:** Integration and error tests that duplicate unit tests or assert nothing: graceful_shutdown, cleanup_removes_all_resources, pipe_permissions, channel_name_derivation_edge_cases, multiple_transports_same_namespace, errors `test_write_to_nonexistent_pipe` (cannot fail since #51; improver mutation proof), and `test_empty_channel_name`. Fold into the #59 removal. status: issue [#64](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/64).
- **S-10:** security finding, details held locally. status: open.

Re-check of earlier quality findings: F-39 (#29), F-40, F-42 (grown, 131 offenses) and F-43 (flog 99.5; #37 extracts nothing) still hold. F-41 is resolved by #47 (the copied setup lives on as F-89/F-100). F-44 is resolved by #32 (#30 needs closing by hand). F-45 is resolved by #33 (its remainder is #56).

## 2026-11-15 (visit 11) — madbot — dependencies audit — develop @ 91197bb

Against smart_message 0.1.0 from GitHub develop @ 2bf06e8 (24 commits past 2aa3dbf; still untagged, not on RubyGems, requires Ruby ≥ 4.0), on Ruby 4.0.9 (dev bundle) and 4.1.0-preview1 (runtime deps only), with the stub `base.rb` and test shim taken off the load path. bundle-audit (advisory-db 5755673) clean. Suite 51/52 against the real gem; the only error is F-50. Framework subscribe+publish delivers end to end; `-w -W:deprecated` clean. Dead-code scan not re-run (it already reached 91197bb).

- **F-101:** hashie 6.0.0 (2026-11-11) is out, but smart_message pins `~> 5.0`. status: open, **upstream** (smart_message's next dependencies audit). Nothing to do here.
- **F-102:** toolchain note: a scratch lock made by Bundler 4.1.0.beta3 on 4.1.0-preview1 locks erb 6.1.0, which 4.0.9 can't install. Make 4.0.9 locks under 4.0.9 (`PATH=~/.rbenv/versions/4.0.9/bin`; `RBENV_VERSION` does not switch Ruby in agent shells). Also: `gemspec path:` puts the stub `lib` back on the load path, so take it off there too when testing against the real gem. status: note.
- Re-checked: F-46 (#6 open), F-47 (needs decision), F-52, F-53 and F-62 hold. **F-51 has grown:** upstream `Plugins.check_transports!` (plugins.rb:43-49, d56db50) now tells users to call `SmartMessage::Transport.create(:named_pipes)`, and that call raises `Unknown transport`. Still needs Dewayne's decision. **F-14:** the `publish(P, msg)` ArgumentError still holds; `subscribe(P, method)` no longer raises at registration, but delivery is unverified. F-20: minitest 6.1.0 gives the same results as 5.27.

Improver (critic read, same commit):

- **F-103:** `subscribe` after `disconnect` (without `connect`) creates the FIFO and starts a reader thread that exits immediately. Nothing raises, and publishers then time out. With `cleanup: true` it raises a misleading ENOENT instead. The main session reran the probe: `{subscribe_after_disconnect: Thread, readers_alive: [false], connected: false}`. Fix: guard `@shutdown` in `subscribe` with a test; lib change, after #37. status: issue [#65](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/65).
- **F-104:** README:163 says the transport uses "Non-blocking I/O"; the reader polls and then calls a blocking `gets`, and the writer does a blocking open under `Timeout`. One-line README fix (low-risk eligible). Overlaps F-21/F-38. status: issue [#66](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/66).
