# Findings: typed_bus

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-09 | First run, at main be4018d (the repo has no `develop`). Library has no eval, shell-outs, YAML/Marshal or ENV reads. Found S-1..S-3 (held locally), plus correctness bugs F-1..F-4 from the probes. |
| dead code | 2026-11-09 (lib only, brief) | debride over lib/examples/test at main be4018d. One true dead item, F-8. Three public-API orphans need a decision (F-13). |
| dependencies | n/a | Not yet run. Triage noted: `async` 2.36 locked vs 2.46 latest (suite green on 2.46); Dependabot alerts disabled; no `dependabot.yml`. |
| adoption | n/a | Not yet run. Improver and triage covered part of it (F-6, F-7, F-11). |
| quality | n/a | Not yet run. No `.rubocop.yml`/`.reek.yml` in the repo. |

Dead-code scan last covered commit: be4018d (main, 2026-11-09; no develop branch exists)

## Findings

### 2026-11-09 (first visit; security audit + dead-code note, main be4018d)

- S-1: security finding, details held locally. Status: open.
- S-2: security finding, details held locally. Status: open.
- S-3: security finding, details held locally. Status: open (informational).
- F-1: A throttled channel at full capacity sleeps forever. `channel.rb:53` calls `apply_throttle!` before `wait_for_capacity`, and at `pending == max_pending` the delay is `1.0/0.0`, so it calls `sleep(Infinity)`. An ack never wakes the publisher. Reproduced independently by the auditor, triage, improver and the main session. Fix: `return if remaining_ratio <= 0` plus a test. Status: issue [#2](https://github.com/MadBomber/typed_bus/issues/2).
- F-2: `max_pending` is never validated. `max_pending: 0` hangs publish forever; `0` plus `throttle` raises `FloatDomainError: NaN` (`channel.rb:205`); `-1` is accepted silently. `Configuration#max_pending=` is a plain accessor. Fix: raise `ArgumentError` unless nil or a positive Integer. Status: open, verified.
- F-3: Unsubscribing during a publish leaks a pending tracker. `channel.rb:56` snapshots `@subscribers.keys`, then `73-74` skip a subscriber removed mid-loop, so that id never gets a Delivery or a timeout. The tracker stays pending forever and permanently uses one `max_pending` slot. Probe: pending_count stays 1 after the timeout window. Fix: snapshot `@subscribers.to_a` and iterate that. Status: open, verified.
- F-4: `publish` outside an Async reactor blocks for the full delivery timeout and dead-letters messages that were acked (`delivery.rb:65` runs `Async { sleep }` as a blocking top-level reactor). With the default timeout this is 30 s per publish. Fix options: raise when there is no current task, or wrap in `Sync {}`. Status: needs Dewayne's decision (raise vs Sync).
- F-5: The Ruby floor `>= 3.2.0` (`typed_bus.gemspec:19`, README, `docs/getting-started/installation.md`) includes EOL Ruby 3.2, and no workflow runs the test suite (only the docs deploys). The taste rule says to drop EOL Rubies (debug_me#23). Status: open. Floor bump is small; the test CI needs a Gemfile layout call (see F-12).
- F-6: The gem ships 68 files and 18.8 MB, mostly `docs/assets/images/typed_bus.gif` (16.6 MB) and `.mp4` (2.1 MB), plus dotfiles, workflows and `examples/temp.md`. Fix: allowlist `spec.files`. Status: issue [#3](https://github.com/MadBomber/typed_bus/issues/3).
- F-7: CHANGELOG says `[0.1.0] - 2026-02-06`, but `version.rb`, tag `v0.0.1` and RubyGems are all 0.0.1 (published 2026-02-07), and the "Unreleased" rename and throttle work already shipped in 0.0.1 (`lib/` identical to the published gem). Fix: retitle as `[0.0.1] - 2026-02-07`, fold Unreleased in, start a fresh Unreleased. Status: open, verified.
- F-8: `Configuration#initialize_copy` (`configuration.rb:67-70`) is an empty override; default `dup` does the same. No callers. Pure removal, same kind as debug_me#36. Status: open, verified.
- F-9: `require "typed_bus"` raises `LoadError: logger` on Ruby 4.0 under Bundler. `logger` is a bundled gem since 4.0 and the gemspec declares only `async`. Reproduced by the main session with a consumer Gemfile holding only `gem "typed_bus", "0.0.1"`. Fix: `spec.add_dependency "logger"`. Status: issue [#1](https://github.com/MadBomber/typed_bus/issues/1).
- F-10: `examples/15_performance.rb:23` does `require "csv"`, which raises `LoadError` on Ruby 3.4+ (csv is a bundled gem). `CSV` is never used (the file writes CSV by hand). `examples/README.md` lists only 00-14. Fix: delete the require and add the README row. Status: open, verified.
- F-11: A standalone `Channel` never records `<name>_published`, although `docs/guides/stats-and-monitoring.md:52-59` and the README say it does. Only `message_bus.rb:73` increments it, and the bus also counts publishes that then raise (type mismatch, closed channel). `channel_test.rb:384` is named for the published counter but asserts only `_delivered`. Fix: record `:published` in `Channel#publish` after validation and drop the bus increment. Rejected publishes would stop counting, which the PR must say. Status: open, verified.
- F-12: Docs deploy collision. `deploy-github-pages.yml` runs `mkdocs gh-deploy --force --clean`, which wipes `yard/` on gh-pages; the YARD workflow restores it only when its own path filters match (gh-pages history shows `/yard/` dropped after bda3250 and restored only by be4018d). The workflow is named "Deploy HTM Documentation" (copy-paste), and the YARD path filter names a `.yardopts` that doesn't exist. Status: open, verified. A small workflow change, but the approach (keep_files vs one merged workflow) is a choice.
- F-13: Public-API orphans (not dead, need a deprecation path to remove): `DeliveryTracker#on_dead_letter`/`fire_dead_letter` (never registered by Channel; documented and tested), `Stats#data` (exposes the mutable internal hash; `to_h` returns a copy; documented), `TB = TypedBus` (a two-letter global alias, tested but undocumented). Status: needs Dewayne's decision.
- F-14: `MessageBus#clear!` docs disagree with code. The lib comment (`message_bus.rb:144`), README:385 and `docs/api/message-bus.md:101` say it closes all channels, but the code calls only `Channel#clear!`, and `docs/guides/graceful-shutdown.md:36-41` says channels stay usable. Status: needs Dewayne's decision (make the code close channels vs treat clear! as a reset and fix three docs).
