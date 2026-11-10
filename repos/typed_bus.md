# typed_bus

Async (fiber) pub/sub gem with typed channels, ACK/NACK delivery, dead letter queues, backpressure and adaptive throttling. `robot_lab` depends on it (`~> 0.0.1`).

## Links

- GitHub: https://github.com/MadBomber/typed_bus
- RubyGems: https://rubygems.org/gems/typed_bus
- Docs: https://madbomber.github.io/typed_bus
- Issues: https://github.com/MadBomber/typed_bus/issues

## Status

_Updated 2026-11-10 04:04 (fourth visit): tend-and-record only — the three PRs from visit 3 (#7/#8/#9) are still open and unapproved, so the review queue is full and no new PR was opened. Tender merged nothing (none approved), confirmed all three still MERGEABLE/CLEAN against `develop`. Responder posted no comments (all 9 open issues are bomberbot-authored with nothing new to add; the three with fixing PRs already link them). Dependencies audit ran (first time): filed [#14](https://github.com/MadBomber/typed_bus/issues/14) (stale async 2.36→2.46 lock, F-29) and [#15](https://github.com/MadBomber/typed_bus/issues/15) (no dependabot.yml, F-28); triage found a fresh red CI on `develop` and filed [#13](https://github.com/MadBomber/typed_bus/issues/13) (YARD deploy fails building ratatui_ruby, F-32). S-32 (dev-tree advisories, shipped gem clean) held locally. aigcm/ratatui_ruby dev-dep bloat (F-31) → inbox (needs decision)._ _Updated 2026-11-10 00:45 (third visit): `develop` now exists (Dewayne created it from `main`), so PRs are unblocked. Opened three PRs against `develop`, each resolving an open issue: [#9](https://github.com/MadBomber/typed_bus/pull/9) (F-1/#2 throttle hang), [#8](https://github.com/MadBomber/typed_bus/pull/8) (F-18/#6 DLQ drain), [#7](https://github.com/MadBomber/typed_bus/pull/7) (F-9/#1 logger dep). None merged as low risk (all touch `lib/` or the gemspec). First adoption audit ran (F-23..F-26); filed [#10](https://github.com/MadBomber/typed_bus/issues/10) (stray examples/temp.md), [#11](https://github.com/MadBomber/typed_bus/issues/11) (no LICENSE), [#12](https://github.com/MadBomber/typed_bus/issues/12) (thin gemspec metadata)._ _Second visit (2026-11-09 16:30): still no `develop`, so blocked for PRs; first quality audit ran (F-15..F-22) and filed [#4](https://github.com/MadBomber/typed_bus/issues/4), [#5](https://github.com/MadBomber/typed_bus/issues/5), [#6](https://github.com/MadBomber/typed_bus/issues/6)._ _First visit:_ **Blocked for PRs: the repo has no `develop` branch** (only `main` and `gh-pages`), so bomberbot couldn't branch from or target it; creating it is Dewayne's step (see `dewayne.md`). Suite green on Ruby 4.0.9 (201 runs, 100% line and branch coverage), but no CI runs it. First security audit ran; filed [#1](https://github.com/MadBomber/typed_bus/issues/1) (published 0.0.1 raises `LoadError: logger` on Ruby 4.0), [#2](https://github.com/MadBomber/typed_bus/issues/2) (throttled channel at capacity sleeps forever) and [#3](https://github.com/MadBomber/typed_bus/issues/3) (18.8 MB gem package).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo, once `develop` exists.

- [#1](https://github.com/MadBomber/typed_bus/issues/1) declare `logger` as a runtime dependency (F-9). **PR open: [#7](https://github.com/MadBomber/typed_bus/pull/7).**
- [#2](https://github.com/MadBomber/typed_bus/issues/2) throttle must not sleep(Infinity) at full capacity (F-1). **PR open: [#9](https://github.com/MadBomber/typed_bus/pull/9).**
- [#3](https://github.com/MadBomber/typed_bus/issues/3) allowlist `spec.files` (F-6).
- [#4](https://github.com/MadBomber/typed_bus/issues/4) unsubscribe-during-publish leaks a pending tracker (F-3).
- [#5](https://github.com/MadBomber/typed_bus/issues/5) validate `max_pending` (F-2).
- [#6](https://github.com/MadBomber/typed_bus/issues/6) `DeadLetterQueue#drain` must keep unprocessed entries when the block raises (F-18). **PR open: [#8](https://github.com/MadBomber/typed_bus/pull/8).**
- [#11](https://github.com/MadBomber/typed_bus/issues/11) add a LICENSE file to match the MIT gemspec (F-23).
- [#12](https://github.com/MadBomber/typed_bus/issues/12) enrich gemspec metadata, drop the duplicate `source_code_uri` (F-24).
- [#10](https://github.com/MadBomber/typed_bus/issues/10) remove stray `examples/temp.md` (F-27).
- [#13](https://github.com/MadBomber/typed_bus/issues/13) fix red YARD-deploy CI on `develop`: drop `bundler-cache` so it stops building ratatui_ruby (F-32). `.github/` change, not low risk.
- [#14](https://github.com/MadBomber/typed_bus/issues/14) `bundle update async` to refresh the stale 2.36 lock to 2.46 (F-29).
- [#15](https://github.com/MadBomber/typed_bus/issues/15) add a `bundler` `.github/dependabot.yml` (F-28).
- F-15: re-check `@closed` after backpressure/throttle waits in `publish` (after #2/#4).
- F-19: make the three assertion-free async tests able to fail.
- F-10: dead `require "csv"` breaks example 15 on Ruby 3.4+.
- F-7: CHANGELOG heading says 0.1.0; released version is 0.0.1.
- F-8: remove the no-op `Configuration#initialize_copy`.

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/typed_bus/pull/9) `madbot/throttle-full-capacity` — fix throttled channel hang at full capacity. Fixes #2 (F-1). Base `develop`, clean review, suite 202/0F. Awaiting Dewayne (lib change).
- [#8](https://github.com/MadBomber/typed_bus/pull/8) `madbot/dlq-drain-raise` — retain entries when drain block raises. Fixes #6 (F-18). Base `develop`, clean review, suite 202/0F. Awaiting Dewayne (lib change).
- [#7](https://github.com/MadBomber/typed_bus/pull/7) `madbot/logger-runtime-dep` — declare `logger` runtime dependency. Fixes #1 (F-9). Base `develop`, clean review, suite 201/0F. Awaiting Dewayne (gemspec change).

## Merged bomberbot PRs

- (none)
