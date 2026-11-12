# smart_message-transport-named_pipes

A Ruby gem (v0.0.1) providing a Unix named pipes (FIFO) transport for the SmartMessage
pub/sub messaging system — fast inter-process communication between processes on the same
machine. Unix-only (needs `mkfifo`). Runtime-depends on `smart_message`. `lib/` is three
files: the transport, a committed test stub `transport/base.rb`, and a `version.rb`.

## Links

- GitHub: https://github.com/MadBomber/smart_message-transport-named_pipes
- RubyGems: https://rubygems.org/gems/smart_message-transport-named_pipes
- Issues: https://github.com/MadBomber/smart_message-transport-named_pipes/issues

## Status

_Updated 2026-11-12 01:10 (visit 4): Dewayne merged all five visit-1/2 PRs (#4/#5/#8/#9/#10) without comment, so new work ran. **Merged (low risk)** [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16) (examples nested-def hoist, Fixes #11) at `6725e5c`; opened [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20) (Fixes #13) and a stacked #15 fix. First **adoption** audit (F-24…F-32, +F-33): README/examples use an `attribute` DSL no smart_message has; filed [#17](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/17) (connect after disconnect), [#18](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/18) (silent message loss), [#19](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/19) (README Related Projects)._

**Visit 3 (2026-11-10):** **skipped new work — review queue full** (#4/#5/#8/#9/#10
still open, unreviewed; `develop` unmoved at 2a49fcc). First **dependencies** audit (F-14…F-23):
against the real smart_message 0.0.17, the framework path works but the transport API the README
teaches does not (F-14), and lowering the #6 floor alone isn't enough (F-18). Filed three verified
lib bugs: [#13](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/13) (unsubscribe wrong dispatcher key), [#14](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/14) (~2s stall per channel
on unsubscribe/disconnect), [#15](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/15) (`unsubscribe!` leaks pipe+reader)._

**Visit 2 (2026-11-10):** _Updated 2026-11-10 (visit 2): `develop` unmoved since visit 1 (2a49fcc); Dewayne hasn't reviewed
#4/#5 yet. Queue was 2 (under the 3-cap), so new work ran. Quality audit ran (F-8…F-13); the
richest find is a verified runtime bug — the INT/TERM signal handler crashes with `ThreadError:
can't be called from trap context` (mutex in trap context), so graceful shutdown is broken
([#12](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/12)). Opened three
PRs against `develop`:
[#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9) CI (Fixes #7, green
3.2–3.4), [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10) README
dependency-claim accuracy (docs, F-7), [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8)
remove dead `publisher_pipe_name` (F-1). **No self-merges** — #10 qualified as low-risk but its
review left a deferred out-of-scope note (sqa#46 taste rule → not self-merged). Filed issues
[#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11) (example nested
defs) and [#12](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/12) (signal
bug). Five bomberbot PRs now await Dewayne (#4/#5/#8/#9/#10). Suite green direct (45→44 on #8);
`bundle install` still broken by the dep floor (#6)._

**Visit 1 (2026-11-10):** created `develop` from `main` at 8d0d2af (repo had only `main`). First
security + dead-code audit (S-1…S-6, F-1…F-4). Opened #3 (README accuracy, **merged low-risk** at
2a49fcc), #5 (VERSION single-source), #4 (stop packaging the test stub). Filed #6 (uninstallable
dep floor) and #7 (no CI).

## Backlog

Well-defined items a single small PR could close.

- **F-5 / [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6):** gemspec `smart_message >= 0.1.0` is unsatisfiable (RubyGems max 0.0.17) → gem
  uninstallable. Needs Dewayne's call on the floor (lower to 0.0.17 vs publish 0.1.0). In inbox.
- **F-4:** single-source the duplicated `VERSION`. **Merged [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5).**
- **S-5:** stop shipping the test stub `transport/base.rb` in the gem. **Merged [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4).**
- **F-6 / [#7](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/7):** add CI that runs the suite directly. **Merged [#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9)** (Fixes #7, green 3.2–3.4).
- **F-7:** README "zero dependencies" wording vs the `smart_message` runtime dep. **Merged [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10).**
- **F-1:** remove dead private `publisher_pipe_name`. **Merged [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8).**
- **F-11 / [#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11):** hoist nested `def`s in `examples/*.rb`. **Merged (low risk) [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16)** at 6725e5c; close #11 by hand.
- **F-8 / [#12](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/12):** signal handler crashes (mutex in trap context) → graceful shutdown broken. Fix needs an opt-in/self-pipe design.
- **F-2:** remove vestigial `publisher_pipes` **public** `attr_reader` — public API →
  needs Dewayne's decision (inbox). (The private method is gone via #8.)
- **F-3:** `mode` / `buffer_size` options are documented but inert → implement or remove (inbox).
- **F-15 / [#13](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/13):** `unsubscribe` uses the Class, not `to_s`, as the dispatcher key → tears down a still-used pipe. One-line fix + test; ready PR when the queue clears. **PR open [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20).**
- **F-22 / [#14](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/14):** unsubscribe/disconnect stall ~2s per idle channel; wake the blocked reader with a nonblocking write-open + per-channel stop flag.
- **F-16 / [#15](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/15):** add `unsubscribe!` override that removes the pipe and stops the reader.
- **F-11 / [#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11):** responder verified the hoist is behaviour-neutral (~56 lines across both examples, `ruby -wc` clean).
- **F-14 / F-18 / F-19:** README transport API vs real Base; smart_message's own unpinned deps; Ruby floor — Dewayne's decisions (inbox).
- **F-10:** newline framing corrupts multi-line payloads → wire-format decision (inbox).
- **F-12:** gem also ships `.github/`, `.envrc`, `Rakefile` (one-line gemspec reject list) — ready PR.
- **F-32:** CHANGELOG `[Unreleased]` lacks merged #10 — one-line low-risk docs PR.
- **F-33 / [#17](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/17):** connect after disconnect leaves the transport dead — small lib fix + test.
- **F-25 / [#19](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/19):** README Related Projects lists nonexistent gems — README-only, low-risk.
- **F-27 / [#18](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/18):** silent message loss under bursty publish (pairs with #14's reader rework).
- **F-24 / F-26 / F-30:** examples/README `attribute` DSL, unreleased-gem docs, throughput claim — Dewayne's decisions (inbox).

## Open bomberbot PRs

- [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20) `madbot/unsubscribe-key` — `unsubscribe` looks up subscribers by `String(message_class)` (as smart_message 0.0.17's Dispatcher keys them); test stub now registers handlers the same way. Base `develop`, CI green 3.2–3.4, review clean round 1, suite 47/0F/9S. Fixes #13. lib → not low risk, awaiting Dewayne. Source: brain/issue (F-15).

## Merged bomberbot PRs

- [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16) `madbot/examples-hoist-defs` — hoist nested handler `def`s in both examples (Fixes #11). **Merged into develop as low risk by madbot** (examples-only, +32/−31, clean review) at `6725e5c`, 2026-11-12.
- [#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9) `madbot/ci-tests-workflow` — merged by Dewayne (squash) 2026-11-10/11.
- [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10) `madbot/readme-deps-accuracy` — merged by Dewayne (squash) 2026-11-10/11.
- [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8) `madbot/remove-dead-publisher-pipe` — merged by Dewayne (squash) 2026-11-10/11.
- [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5) `madbot/version-single-source` — merged by Dewayne (squash) 2026-11-10/11.
- [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4) `madbot/exclude-stub-from-gem` — merged by Dewayne (squash) 2026-11-10/11.
- [#3](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/3) `madbot/readme-accuracy` — corrected the README channel-derivation example and stale `Running Tests` file paths. **Merged into develop as low risk by madbot** (docs-only, clean review) at `2a49fcc`. Source: improver.
