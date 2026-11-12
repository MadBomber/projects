# Findings — smart_message-transport-named_pipes

Audit backlog for this repo. Numbered findings persist; statuses update as they become
issues (`issue #N`), PRs, or are resolved. Security details are held locally in
`notes/security/smart_message-transport-named_pipes.md`; only placeholders appear here.

## Audits table

| audit | last run | commit | result |
|-------|----------|--------|--------|
| security (+dead-code) | 2026-11-10 | 8d0d2af | S-1…S-6 (5 open local/correctness, 1 unverified-theoretical); F-1…F-4 dead-code |
| quality | 2026-11-10 | 2a49fcc | F-8…F-13 (RuboCop 137 offenses baseline; 9 test skips); F-8→issue #12, F-11→issue #11 |
| dependencies | 2026-11-10 | 2a49fcc | F-14…F-21 (+F-22/F-23 from triage/improver); stub checked against real smart_message 0.0.17 — framework path works, README transport API doesn't; minitest 6.1.0 blocked by `~> 5.16`; Ruby floor 3.2 is EOL. F-15→#13, F-22→#14, F-16→#15 |
| adoption | 2026-11-12 | b2042fc | F-24…F-32 (README/examples never ran against any smart_message — `attribute` DSL; Related Projects names nonexistent gems; gem unpublished/untagged; silent EPIPE message loss); +F-33 (improver) connect-after-disconnect; no mkdocs; no llms.txt; gem-skill blocked by #6 |

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
