# typed_bus

Async (fiber) pub/sub gem with typed channels, ACK/NACK delivery, dead letter queues, backpressure and adaptive throttling. `robot_lab` depends on it (`~> 0.0.1`).

## Links

- GitHub: https://github.com/MadBomber/typed_bus
- RubyGems: https://rubygems.org/gems/typed_bus
- Docs: https://madbomber.github.io/typed_bus
- Issues: https://github.com/MadBomber/typed_bus/issues

## Status

_Updated 2026-11-09 (first visit)._ **Blocked for PRs: the repo has no `develop` branch** (only `main` and `gh-pages`), so bomberbot couldn't branch from or target it; creating it is Dewayne's step (see `dewayne.md`). Suite green on Ruby 4.0.9 (201 runs, 100% line and branch coverage), but no CI runs it. First security audit ran; filed [#1](https://github.com/MadBomber/typed_bus/issues/1) (published 0.0.1 raises `LoadError: logger` on Ruby 4.0), [#2](https://github.com/MadBomber/typed_bus/issues/2) (throttled channel at capacity sleeps forever) and [#3](https://github.com/MadBomber/typed_bus/issues/3) (18.8 MB gem package).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo, once `develop` exists.

- [#1](https://github.com/MadBomber/typed_bus/issues/1) declare `logger` as a runtime dependency (F-9).
- [#2](https://github.com/MadBomber/typed_bus/issues/2) throttle must not sleep(Infinity) at full capacity (F-1).
- [#3](https://github.com/MadBomber/typed_bus/issues/3) allowlist `spec.files` (F-6).
- F-3: unsubscribe-during-publish leaks a pending tracker.
- F-2: validate `max_pending`.
- F-10: dead `require "csv"` breaks example 15 on Ruby 3.4+.
- F-7: CHANGELOG heading says 0.1.0; released version is 0.0.1.
- F-8: remove the no-op `Configuration#initialize_copy`.

## Open bomberbot PRs

- (none)

## Merged bomberbot PRs

- (none)
