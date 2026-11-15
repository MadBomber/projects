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

_Updated 2026-11-15 04:45 (visit 11): **skipped new work: review queue full** (#57/#55/#58 still unreviewed; `develop` unmoved at 91197bb). Dependencies audit found F-101 (upstream) and F-102 (note); F-51 has grown. Filed [#65](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/65) and [#66](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/66)._

**Visit 10b (2026-11-15 04:10):** skipped (queue full); quality audit F-96…F-100, S-10 (local); filed #62, #63, #64.

**Visit 10 (2026-11-15 03:26):** **skipped new work — review queue full** (#57/#55/#58 open, unreviewed; `develop` unmoved at 91197bb); security re-audit (S-9 new, local; S-7 resolved); filed [#59](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/59), [#60](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/60), [#61](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/61).

**Visit 9 (2026-11-15 02:17):** `develop` CI was red since #52 (signal-test child lacked `-Itest`); bomberbot opened [#57](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/57) (fix, green on 3.2–4.0), [#55](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/55) (gemspec metadata, lanet#21 pattern) and [#58](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/58) (README Running Tests), none low risk (gemspec / review cap hit); merge #57 first. Adoption audit F-84…F-89; filed [#56](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/56)._

**Visit 8 (2026-11-13 ~02:50, cut off before writing brain):** opened #47/#48/#49; Dewayne squash-merged them 2026-11-13 21:29 with his #31–#38/#50–#54 and our #39. Run dir moved to `bomberbot-interrupted/` on 2026-11-15.

**Visit 7 (2026-11-13 01:10):** Dewayne merged all four waiting PRs (#20/#21/#22/#27; #21 landed only in its stale stacked base, his #34 redoes it) and opened his own #31–#38 for eight issues; madbot **merged (low risk)** [#43](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/43) (README no-subscriber publish, 4a27bc3) and [#44](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/44) (test hygiene, b162732), opened [#39](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/39) (gemspec packaging + version), ran the dependencies audit against smart_message 0.1.0 (F-46…F-54), and filed #40/#41/#42._

**Visit 6 (2026-11-12 08:22):** **skipped new work — review queue full** (#20/#21/#22/#27 open, unreviewed, all rebased and green). Quality audit re-run (F-39…F-44, +F-45); filed [#28](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/28) (ENV read at require), [#29](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/29) (shared FIFO deleted under other subscribers), [#30](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/30) (CI lacks Ruby 4.0)._

**Visit 5 (2026-11-12 06:55):** queue was 2 (#20/#21, rebased, unreviewed), so new work ran. Opened [#22](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/22) (README Related Projects, Fixes #19; held — review left an out-of-scope note) and [#27](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/27) (reconnect, Fixes #17); **merged (low risk)** [#26](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/26) (test assertions) at `52f8e45`. Security audit re-run (S-7 local, F-34…F-38); filed [#23](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/23), [#24](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/24), [#25](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/25)._

**Visit 4 (2026-11-12):** Dewayne merged all five visit-1/2 PRs (#4/#5/#8/#9/#10) without comment, so new work ran. **Merged (low risk)** [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16) (examples nested-def hoist, Fixes #11) at `6725e5c`; opened [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20) (Fixes #13) and stacked [#21](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/21) on it (Fixes #15). First **adoption** audit (F-24…F-32, +F-33): README/examples use an `attribute` DSL no smart_message has; filed [#17](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/17) (connect after disconnect), [#18](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/18) (silent message loss), [#19](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/19) (README Related Projects)._

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
- **F-15 / [#13](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/13):** `unsubscribe` uses the Class, not `to_s`, as the dispatcher key → tears down a still-used pipe. One-line fix + test; ready PR when the queue clears. **Merged [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20).**
- **F-22 / [#14](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/14):** unsubscribe/disconnect stall ~2s per idle channel; wake the blocked reader with a nonblocking write-open + per-channel stop flag.
- **F-16 / [#15](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/15):** add `unsubscribe!` override that removes the pipe and stops the reader. [#21](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/21) merged only into its stale base; **Dewayne's #34 redoes it** (not yet on develop).
- **F-11 / [#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11):** responder verified the hoist is behaviour-neutral (~56 lines across both examples, `ruby -wc` clean).
- **F-14 / F-18 / F-19:** README transport API vs real Base; smart_message's own unpinned deps; Ruby floor — Dewayne's decisions (inbox).
- **F-10:** newline framing corrupts multi-line payloads → wire-format decision (inbox).
- **F-12:** gem also ships `.github/`, `.envrc`, `Rakefile` (one-line gemspec reject list) — **PR open [#39](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/39)** (with the F-4 residual: gemspec reads VERSION from version.rb as text).
- **F-32:** CHANGELOG `[Unreleased]` lacks merged #10 — one-line low-risk docs PR.
- **F-33 / [#17](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/17):** connect after disconnect leaves the transport dead — small lib fix + test. **Merged [#27](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/27).**
- **F-25 / [#19](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/19):** README Related Projects lists nonexistent gems — README-only, low-risk. **Merged [#22](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/22).**
- **F-27 / [#18](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/18):** silent message loss under bursty publish (pairs with #14's reader rework).
- **F-34 / [#23](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/23):** a raising `receive` drops the messages buffered with it and stalls ~1s — per-message rescue + test (same loop as #14/#18).
- **F-36 / [#24](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/24):** `disconnect` with `cleanup: false` leaves reader threads running — stop readers before the cleanup guard.
- **F-35 / [#25](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/25):** CI workflow lacks `permissions: contents: read` (push needs `workflow` scope/SSH).
- **F-24 / F-26 / F-30:** examples/README `attribute` DSL, unreleased-gem docs, throughput claim — Dewayne's decisions (inbox).

- **F-45 / [#28](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/28):** env vars read at require time — `default_options` merges ENV at call time + real test. Small lib PR when the queue clears.
- **F-39 / [#29](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/29):** one subscriber's disconnect deletes the shared FIFO; ownership depends on F-40 (Dewayne: pub/sub fan-out vs work queue).
- **F-44 / [#30](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/30):** Ruby 4.0 is in the CI matrix on `develop` (#32); the issue only needs closing by hand (dewayne.md).
- **F-41 / [#42](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/42):** duplicate write-timeout test (integration copy, ~2s) + copied setup — test-only removal; not low risk (deletes a test).
- **F-43:** extract `deliver_lines` from `start_pipe_reader` as part of the #23 fix.
- **F-73 / [#40](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/40):** publish after FIFO removal creates a regular file that blocks re-subscribe — after #18/#29 settle.
- **F-48 / [#41](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/41):** bare `transport/base` require → NameError under smart_message 0.1.0; pairs with F-49/F-50 (test against real gem) after #6.
- **F-47:** Ruby floor to 4.0 (smart_message 0.1.0 requires it) — gemspec/README/.rubocop.yml PR once Dewayne confirms.
- In flight (Dewayne, [#37](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/37), #14): `named_pipes.rb`, `named_pipes_test.rb`, CHANGELOG — no bot lib work there until it lands. #31–#36, #38 merged.
- **F-84:** `develop` CI red since #52 — **PR open [#57](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/57)**.
- **F-85:** README Running Tests lists 3/5 files — **PR open [#58](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/58)**.
- **F-60 metadata / F-74:** gemspec `bug_tracker_uri`/`documentation_uri`/MFA, drop duplicate `homepage_uri`, indent — **PR open [#55](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/55)**.
- **F-87 / [#56](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/56):** `DEFAULT_CONFIG` still reads env at require — after #37.
- **F-88:** move `lib/.../transport/base.rb` stub to `test/` (after #57; folds into F-49). **F-89:** shared child-ruby `-I` helper in `test_helper.rb`. **F-90:** signal test's `popen3` can hang if an assertion/Timeout fires with the child alive (kill in `ensure`); drain stderr while it runs.
- **F-91:** CI loop stops at first failing file under `bash -e` and globs only `test/smart_message/transport/` (Rakefile: `test/**`); workflow → Dewayne. README Development "run `bundle install`" line contradicts #6.
- **F-92 / [#59](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/59):** two errors tests that can never fail — test-only removal (~−37), not low risk (deletes tests). Ready PR once the queue clears.
- **F-93 / [#60](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/60):** stub logger never yields log blocks — ~4-line test-stub guard; pair with F-88.
- **F-94 / [#61](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/61):** class names differing by case or `::` share one pipe and misroute — `subscribe` collision raise is small; naming scheme → Dewayne (inbox).
- **F-98 / [#62](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/62):** `disconnect`/`at_exit` rmdirs the shared namespace dir, breaking other transports. `mkdir_p` + drop rmdir + test; lib, after #37.
- **F-99 / [#63](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/63):** publish error paths untested. Two tests in errors_test; test-only, adds tests (low-risk eligible). Best next PR when the queue clears.
- **F-100 / [#64](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/64):** duplicate or assertion-free integration/error tests; fold into the #59 removal PR.
- **F-96 / F-97:** large concurrent publishes interleave, and a timed-out partial write corrupts the next message. Framing decision for Dewayne (inbox, with F-10).
- **F-103 / [#65](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/65):** `subscribe` after `disconnect` gives a dead subscriber. Fix: `@shutdown` guard plus a test; lib change, after #37.
- **F-104 / [#66](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/66):** README:163 "Non-blocking I/O" claim. One-line README fix, low-risk eligible; good next docs PR once #58 merges (same file).
- **S-10 (local):** neutral "cap reader line length at buffer_size" lib PR after #37 lands.
- **S-9 (local):** neutral "verify the opened pipe path is a FIFO" lib PR after #37 lands.
- Next-PR ranking: now (no #37 overlap): F-99/#63, F-93/#60, #59+#64 removal. After #37 lands: F-98/#62, F-87/#56, F-21/F-38 ENXIO removal, F-32 CHANGELOG gaps (#10/#39/#48), F-88, F-89+F-90.

## Open bomberbot PRs

- [#57](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/57) `bomberbot/signal-test-load-path` — test: give the signal test child the test load path. Fixes red develop CI (#52×#50). Test-only, +8/−2, CI green 3.2–4.0. Review hit the 3-round cap (round 3 clean) with deferred pre-existing notes (popen3 hang, stderr drain, shared helper) → not self-merged. Source: CI. **Merge first.**
- [#55](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/55) `bomberbot/gemspec-metadata` — build: add bug tracker, docs and MFA gemspec metadata (lanet#21 pattern; +1 CHANGELOG `### Changed`, merge-tree clean vs #37). Gemspec → not low risk. Review clean round 1. CI red only on the signal test until #57. Source: brain (inbox, F-60/F-74).
- [#58](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/58) `bomberbot/readme-running-tests` — docs: run the whole suite in README Running Tests. README +12/−5; review hit cap with deferred notes → not self-merged. CI red only on the signal test until #57. Source: adoption (improver, F-85).

## Merged bomberbot PRs

- [#49](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/49) — docs: match examples no-subscriber tip to the code (0255bf0). Squash-merged by Dewayne 2026-11-13 (opened by the cut-off visit 8).
- [#48](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/48) — docs: list smart_message 0.1.0 built-in transports (c40927b). Squash-merged by Dewayne 2026-11-13 (opened by the cut-off visit 8).
- [#47](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/47) — test: drop duplicate no-reader write timeout test, Fixes #42 (a31e074). Squash-merged by Dewayne 2026-11-13 (opened by the cut-off visit 8).
- [#39](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/39) — build: tidy gemspec packaging and version source (bedd6d5). Squash-merged by Dewayne 2026-11-13.
- [#44](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/44) `madbot/test-hygiene` — errors/integration tests stay in their temp dirs, teardown disconnects, two needless skips removed (F-70/71/72). **Merged into develop as low risk by madbot** (test-only, +36/−30, no tests deleted, clean review) at `b162732`, 2026-11-13.
- [#43](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/43) `madbot/readme-no-subscriber` — README says publish with no subscriber drops the message; pipe with no reader times out (F-61). **Merged into develop as low risk by madbot** (README +2, clean review) at `4a27bc3`, 2026-11-13.
- [#27](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/27) `madbot/reconnect-after-disconnect` — merged by Dewayne (rebase) 2026-11-12 (bebe33b, f49d642). Fixes #17.
- [#22](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/22) `madbot/readme-related-projects` — merged by Dewayne (merge commit c143600) 2026-11-13, with a comment that `-redis` may already ship in smart_message. Fixes #19.
- [#21](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/21) `madbot/unsubscribe-bang` — merged by Dewayne **into its stale base `madbot/unsubscribe-key`**, never develop; his #34 carries the change. Branch deleted visit 7.
- [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20) `madbot/unsubscribe-key` — merged by Dewayne (rebase) 2026-11-12 (706a382, 1ba06b4). Fixes #13.
- [#26](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/26) `madbot/test-cleanup-assertions` — replace placeholder `assert true` with real cleanup assertions, drop three unused vars (F-37). **Merged into develop as low risk by madbot** (test-only, +2/−5, clean review) at `52f8e45`, 2026-11-12.
- [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16) `madbot/examples-hoist-defs` — hoist nested handler `def`s in both examples (Fixes #11). **Merged into develop as low risk by madbot** (examples-only, +32/−31, clean review) at `6725e5c`, 2026-11-12.
- [#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9) `madbot/ci-tests-workflow` — merged by Dewayne (squash) 2026-11-10/11.
- [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10) `madbot/readme-deps-accuracy` — merged by Dewayne (squash) 2026-11-10/11.
- [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8) `madbot/remove-dead-publisher-pipe` — merged by Dewayne (squash) 2026-11-10/11.
- [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5) `madbot/version-single-source` — merged by Dewayne (squash) 2026-11-10/11.
- [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4) `madbot/exclude-stub-from-gem` — merged by Dewayne (squash) 2026-11-10/11.
- [#3](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/3) `madbot/readme-accuracy` — corrected the README channel-derivation example and stale `Running Tests` file paths. **Merged into develop as low risk by madbot** (docs-only, clean review) at `2a49fcc`. Source: improver.
