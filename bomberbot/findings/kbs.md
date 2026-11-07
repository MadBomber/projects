# Findings: kbs

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | 2026-11-06 | First run. `bundle outdated`: only major bumps are available (minitest 6, simplecov 1.x, redis 6), so none was taken. Ruby 4 readiness greps were clean. Suite green on 4.1.0-preview1 and 4.0.9. |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: (none; the 2026-11-06 run spent its budget on the dependencies audit)

## Findings

### 2026-11-06 (dependencies audit)

- **F-1: `rake test` ran zero tests.** `Minitest::TestTask` loads `minitest/autorun` before `test_helper` starts SimpleCov, so SimpleCov's at_exit hook fails the 35% gate and the run is skipped. Status: PR'd ([#6](https://github.com/MadBomber/kbs/pull/6)).
- **F-2: Redis tests flush DB 15 of whatever Redis is on `localhost:6379`.** The URL is hard-coded in `test/test_redis_store.rb` and `test/test_hybrid_store.rb`. Fix: read the URL from an env var such as `KBS_TEST_REDIS_URL`, and skip the tests when it is unset. Status: open (good next PR; F-1 makes these tests run under rake).
- **F-3: `redis_available?` only rescues LoadError, CannotConnectError and TimeoutError.** A Redis that needs AUTH, or that rejects DB 15, makes setup raise errors instead of skipping. Status: open (fold into F-2).
- **F-4: `require "kbs/blackboard"` hard-requires the `redis` gem.** The require chain is `blackboard.rb` to `persistence/redis_store.rb` to `require 'redis'`, but `redis` is only a development dependency in the gemspec, so a user without the redis gem gets a LoadError even when using only SQLite. Seen in code; not yet reproduced in a gem environment without redis. Fix options: lazy-require redis inside the Redis classes, or make redis a runtime dependency. Status: unverified.
- **F-5: Unused Gemfile entries.** Nothing in the repo requires `aigcm` or `debug_me` (grep over all `.rb` files). Meanwhile `examples/ai_enhanced_kbs*.rb` require `ruby_llm`, which the Gemfile doesn't list. Status: open (development-only, not public API).
- **F-6: test/README.md drift.** It documents a hand-written `ruby -Ilib:test -e ...` loader and an 80%/70% coverage gate, while `test_helper.rb` enforces 35% with no per-file gate. README.md and docs/installation.md say `rake test`. Status: open.
- **F-7: The 35% coverage gate fails narrow runs.** Single-file runs, narrow filters and `rake test:deps` fail on coverage alone, and SimpleCov result merging (600s) makes the outcome depend on recent runs. This is existing policy in `test_helper.rb`. Status: needs Dewayne's decision (keep it, enforce it only on full runs, or drop it).
- **F-8: No test CI.** The only workflow deploys docs on `docs/**` pushes to main and develop. Status: open (adding a test workflow is an obvious next step once F-2 lands).
