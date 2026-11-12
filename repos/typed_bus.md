# typed_bus

Async (fiber) pub/sub gem with typed channels, ACK/NACK delivery, dead letter queues, backpressure and adaptive throttling. `robot_lab` depends on it (`~> 0.0.1`).

## Links

- GitHub: https://github.com/MadBomber/typed_bus
- RubyGems: https://rubygems.org/gems/typed_bus
- Docs: https://madbomber.github.io/typed_bus
- Issues: https://github.com/MadBomber/typed_bus/issues

## Status

_Updated 2026-11-12 06:25 (ninth visit): skipped new work, review queue full (#28/#29/#30 still unapproved, green, 0 behind); dead-code audit ran at develop 0f94a6c (F-64..F-66); filed [#34](https://github.com/MadBomber/typed_bus/issues/34) (F-70), [#35](https://github.com/MadBomber/typed_bus/issues/35) (F-64), [#36](https://github.com/MadBomber/typed_bus/issues/36) (F-71)._ _Updated 2026-11-12 05:10 (eighth visit): skipped new work, review queue full (#28/#29/#30 unapproved, green, merge cleanly together); adoption audit ran (F-57..F-60); filed [#31](https://github.com/MadBomber/typed_bus/issues/31) (F-54), [#32](https://github.com/MadBomber/typed_bus/issues/32) (F-57), [#33](https://github.com/MadBomber/typed_bus/issues/33) (F-61)._ _Updated 2026-11-12 01:20 (seventh visit): Dewayne merged #22/#23/#24, then fixed every other open issue directly on `develop` (e148a51..0f94a6c), so CI is green and the old Backlog is done; opened [#30](https://github.com/MadBomber/typed_bus/pull/30) (F-15), [#29](https://github.com/MadBomber/typed_bus/pull/29) (F-49) and [#28](https://github.com/MadBomber/typed_bus/pull/28) (CHANGELOG, F-50/F-7), none low risk; second quality audit ran; filed [#25](https://github.com/MadBomber/typed_bus/issues/25)-[#27](https://github.com/MadBomber/typed_bus/issues/27)._ _Updated 2026-11-11 21:30 (sixth visit): Dewayne merged #7/#8/#9 into `develop`, which cleared the queue. Opened [#23](https://github.com/MadBomber/typed_bus/pull/23) (fixes a DLQ drain regression from our #8, F-46), [#22](https://github.com/MadBomber/typed_bus/pull/22) (Fixes #16) and [#24](https://github.com/MadBomber/typed_bus/pull/24) (Fixes #3); none qualified as low risk. The second security audit ran (S-33/S-34 held locally). Filed [#19](https://github.com/MadBomber/typed_bus/issues/19) (example 15 fails on Ruby 3.4+, F-10), [#20](https://github.com/MadBomber/typed_bus/issues/20) (no test CI) and [#21](https://github.com/MadBomber/typed_bus/issues/21) (empty `initialize_copy`, F-8). YARD deploy still red on `develop` (#13)._ _Updated 2026-11-10 04:55 (fifth visit): tend-and-record only again, because the review queue is full (#7/#8/#9 still open, unapproved, MERGEABLE, unchanged). A full dead-code audit ran (F-33..F-35, no dead lib code beyond F-8). Filed [#16](https://github.com/MadBomber/typed_bus/issues/16) (subscribe without a block deadlocks publish, F-41), [#17](https://github.com/MadBomber/typed_bus/issues/17) (logging examples print no DEBUG, F-40) and [#18](https://github.com/MadBomber/typed_bus/issues/18) (three tests that can't fail, F-19). No issue comments needed._ _Updated 2026-11-10 04:04 (fourth visit): tend-and-record only — the three PRs from visit 3 (#7/#8/#9) are still open and unapproved, so the review queue is full and no new PR was opened. Tender merged nothing (none approved), confirmed all three still MERGEABLE/CLEAN against `develop`. Responder posted no comments (all 9 open issues are bomberbot-authored with nothing new to add; the three with fixing PRs already link them). Dependencies audit ran (first time): filed [#14](https://github.com/MadBomber/typed_bus/issues/14) (stale async 2.36→2.46 lock, F-29) and [#15](https://github.com/MadBomber/typed_bus/issues/15) (no dependabot.yml, F-28); triage found a fresh red CI on `develop` and filed [#13](https://github.com/MadBomber/typed_bus/issues/13) (YARD deploy fails building ratatui_ruby, F-32). S-32 (dev-tree advisories, shipped gem clean) held locally. aigcm/ratatui_ruby dev-dep bloat (F-31) → inbox (needs decision)._ _Updated 2026-11-10 00:45 (third visit): `develop` now exists (Dewayne created it from `main`), so PRs are unblocked. Opened three PRs against `develop`, each resolving an open issue: [#9](https://github.com/MadBomber/typed_bus/pull/9) (F-1/#2 throttle hang), [#8](https://github.com/MadBomber/typed_bus/pull/8) (F-18/#6 DLQ drain), [#7](https://github.com/MadBomber/typed_bus/pull/7) (F-9/#1 logger dep). None merged as low risk (all touch `lib/` or the gemspec). First adoption audit ran (F-23..F-26); filed [#10](https://github.com/MadBomber/typed_bus/issues/10) (stray examples/temp.md), [#11](https://github.com/MadBomber/typed_bus/issues/11) (no LICENSE), [#12](https://github.com/MadBomber/typed_bus/issues/12) (thin gemspec metadata)._ _Second visit (2026-11-09 16:30): still no `develop`, so blocked for PRs; first quality audit ran (F-15..F-22) and filed [#4](https://github.com/MadBomber/typed_bus/issues/4), [#5](https://github.com/MadBomber/typed_bus/issues/5), [#6](https://github.com/MadBomber/typed_bus/issues/6)._ _First visit:_ **Blocked for PRs: the repo has no `develop` branch** (only `main` and `gh-pages`), so bomberbot couldn't branch from or target it; creating it is Dewayne's step (see `dewayne.md`). Suite green on Ruby 4.0.9 (201 runs, 100% line and branch coverage), but no CI runs it. First security audit ran; filed [#1](https://github.com/MadBomber/typed_bus/issues/1) (published 0.0.1 raises `LoadError: logger` on Ruby 4.0), [#2](https://github.com/MadBomber/typed_bus/issues/2) (throttled channel at capacity sleeps forever) and [#3](https://github.com/MadBomber/typed_bus/issues/3) (18.8 MB gem package).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

Done on `develop` (close on release): #1-#6, #10-#21 (Dewayne's direct commits e148a51..0f94a6c plus our #7/#8/#9/#22/#23/#24), F-46.

- F-15: re-check `@closed` after the throttle/backpressure waits in `publish`. **PR open: [#30](https://github.com/MadBomber/typed_bus/pull/30).**
- F-49: `DeadLetterQueue#drain` re-queues the handled entry on `break`. **PR open: [#29](https://github.com/MadBomber/typed_bus/pull/29).**
- F-50/F-7: CHANGELOG drain bullet stale, `[0.1.0]` heading should be `[0.0.1] - 2026-02-07`. **PR open: [#28](https://github.com/MadBomber/typed_bus/pull/28).**
- [#25](https://github.com/MadBomber/typed_bus/issues/25) docs "Running All Examples" loop breaks at example 15, which has no index row (F-26). `docs/**` deploys from `develop`, so not low risk.
- [#26](https://github.com/MadBomber/typed_bus/issues/26) DLQ docs pages don't describe drain's snapshot/keep-on-raise behaviour (F-47). Do after #29 settles.
- [#27](https://github.com/MadBomber/typed_bus/issues/27) `timeout: nil` undocumented and logs `timeout=s` (F-53).
- [#31](https://github.com/MadBomber/typed_bus/issues/31) F-54: backpressure DEBUG logs "waiting for capacity" even when it doesn't wait (small lib PR; different hunk from #30).
- [#32](https://github.com/MadBomber/typed_bus/issues/32) F-57: error-handling guide and Configuration API page omit the new `ArgumentError`s (docs).
- [#33](https://github.com/MadBomber/typed_bus/issues/33) F-61: validate `throttle` in `Configuration` like `max_pending` (lib, configuration.rb only).
- F-58/F-59: Requirements omit `logger`, License unlinked, mkdocs copyright 2025 (README+docs).
- F-62: "Unknown channel" message should show `name.inspect` and registered names.
- F-63: delete unused 16.6 MB `docs/assets/images/typed_bus.gif` (removal).
- F-35/F-55: rename `test/async_typed_bus_test.rb`, drop rspec `.gitignore` lines, assert in `test_close_all_on_empty_bus` (tests only, low-risk candidate).
- [#34](https://github.com/MadBomber/typed_bus/issues/34) F-70: validate `type:` in `Channel#initialize` (lib, ArgumentError instead of a bare TypeError on every publish).
- [#35](https://github.com/MadBomber/typed_bus/issues/35) F-64: stale unsubscribe-mid-publish test (tests only; pair with F-35/F-55 for a low-risk test PR).
- [#36](https://github.com/MadBomber/typed_bus/issues/36) F-71: record nack/dead-letter stats before the DLQ callback runs (lib, `channel.rb` on_nack; same file as #30, different hunk).
- F-33 remainder: drop `rss_kb`/`baseline_rss_kb` readers in `examples/15_performance.rb` (examples only).
- F-22: `publish` YARD `@return` should be `[DeliveryTracker, nil]`.

## Open bomberbot PRs

- [#30](https://github.com/MadBomber/typed_bus/pull/30) `madbot/publish-closed-recheck`: `publish` re-checks `closed?` after the throttle and backpressure waits (private `ensure_open!`), so a blocked publisher raises instead of delivering into a closed channel (F-15). Review clean (1 round), suite 226/0F, CI green. 11 RuboCop default-config offenses that match house style (no `.rubocop.yml`, F-52), explained in the PR. Awaiting Dewayne (lib).
- [#29](https://github.com/MadBomber/typed_bus/pull/29) `madbot/drain-break-keeps-handled`: `drain` counts an entry handled before yielding and un-counts it only on an exception, so `break` no longer re-queues it (F-49, follow-up to #23). Review 1 round (one finding rejected: a killed thread now counts as handled; written up in the PR), suite 225/0F, CI green. Awaiting Dewayne (lib).
- [#28](https://github.com/MadBomber/typed_bus/pull/28) `madbot/changelog-accuracy`: CHANGELOG drain bullet, LICENSE note, `[0.0.1] - 2026-02-07` heading with rename/throttle moved under it (F-50, F-7). Review clean (1 round), CI green. Not low risk (rewrites a released heading). Awaiting Dewayne.

## Merged bomberbot PRs

- [#22](https://github.com/MadBomber/typed_bus/pull/22) `bomberbot/subscribe-requires-block` (F-41, #16). Merged by Dewayne 2026-11-12 04:41Z (c311c81, squash/rebase).
- [#23](https://github.com/MadBomber/typed_bus/pull/23) `bomberbot/dlq-drain-snapshot` (F-46). Merged by Dewayne 2026-11-12 (f5f634c).
- [#24](https://github.com/MadBomber/typed_bus/pull/24) `bomberbot/gemspec-files-allowlist` (F-6, #3). Merged by Dewayne 2026-11-12 (36793e9).
- [#7](https://github.com/MadBomber/typed_bus/pull/7) `madbot/logger-runtime-dep`: declare `logger` runtime dependency (F-9, #1). Merged by Dewayne into `develop` 2026-11-10 (merge commit 2a4a373).
- [#8](https://github.com/MadBomber/typed_bus/pull/8) `madbot/dlq-drain-raise`: retain DLQ entries when the drain block raises (F-18, #6). Merged by Dewayne 2026-11-10 (7975b22). Introduced regression F-46.
- [#9](https://github.com/MadBomber/typed_bus/pull/9) `madbot/throttle-full-capacity`: throttled channel hang at full capacity (F-1, #2). Merged by Dewayne 2026-11-10 (0fe5860).
