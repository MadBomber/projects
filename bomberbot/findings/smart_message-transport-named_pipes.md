# Findings — smart_message-transport-named_pipes

Audit backlog for this repo. Numbered findings persist; statuses update as they become
issues (`issue #N`), PRs, or are resolved. Security details are held locally in
`notes/security/smart_message-transport-named_pipes.md`; only placeholders appear here.

## Audits table

| audit | last run | commit | result |
|-------|----------|--------|--------|
| security (+dead-code) | 2026-11-10 | 8d0d2af | S-1…S-6 (5 open local/correctness, 1 unverified-theoretical); F-1…F-4 dead-code |
| quality | 2026-11-10 | 2a49fcc | F-8…F-13 (RuboCop 137 offenses baseline; 9 test skips); F-8→issue #12, F-11→issue #11 |

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
