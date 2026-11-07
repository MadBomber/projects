# Findings: kbs

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | 2026-11-07 | Quick scan only (debride over lib/ at 7381f82): every candidate is public API (attr readers, DSL helpers, Blackboard aliases), so nothing removable without a deprecation decision. Not the full audit. |
| dependencies | 2026-11-06 | First run. `bundle outdated`: only major bumps are available (minitest 6, simplecov 1.x, redis 6), so none was taken. Ruby 4 readiness greps were clean. Suite green on 4.1.0-preview1 and 4.0.9. |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: e9c0712 (2026-11-07 second run; 7381f82..e9c0712 touched only test/, docs and CHANGELOG, nothing in lib/)

## Findings

### 2026-11-06 (dependencies audit)

- **F-1: `rake test` ran zero tests.** `Minitest::TestTask` loads `minitest/autorun` before `test_helper` starts SimpleCov, so SimpleCov's at_exit hook fails the 35% gate and the run is skipped. Status: merged ([#6](https://github.com/MadBomber/kbs/pull/6), 2026-11-07).
- **F-2: Redis tests flush DB 15 of whatever Redis is on `localhost:6379`.** The URL is hard-coded in `test/test_redis_store.rb` and `test/test_hybrid_store.rb`. Fix: read the URL from an env var such as `KBS_TEST_REDIS_URL`, and skip the tests when it is unset. Status: merged ([#7](https://github.com/MadBomber/kbs/pull/7), 2026-11-07); the tests now skip unless `KBS_TEST_REDIS_URL` is set.
- **F-3: `redis_available?` only rescues LoadError, CannotConnectError and TimeoutError.** A Redis that needs AUTH, or that rejects DB 15, makes setup raise errors instead of skipping. Status: merged ([#7](https://github.com/MadBomber/kbs/pull/7), 2026-11-07); with the opt-in, no local Redis is touched unless named, and an opted-in bad URL errors loudly by design.
- **F-4: `require "kbs/blackboard"` hard-requires the `redis` gem.** The require chain is `blackboard.rb` to `persistence/redis_store.rb` to `require 'redis'`, but `redis` is only a development dependency in the gemspec, so a user without the redis gem gets a LoadError even when using only SQLite. Seen in code; not yet reproduced in a gem environment without redis. Fix options: lazy-require redis inside the Redis classes, or make redis a runtime dependency. Status: unverified.
- **F-5: Unused Gemfile entries.** Nothing in the repo requires `aigcm` or `debug_me` (grep over all `.rb` files). Meanwhile `examples/ai_enhanced_kbs*.rb` require `ruby_llm`, which the Gemfile doesn't list. Status: open (development-only, not public API). Since [#8](https://github.com/MadBomber/kbs/pull/8), every CI leg installs `aigcm` and its `ruby_llm` tree too, so this now costs CI time as well.
- **F-6: test/README.md drift.** It documents a hand-written `ruby -Ilib:test -e ...` loader and an 80%/70% coverage gate, while `test_helper.rb` enforces 35% with no per-file gate. README.md and docs/installation.md say `rake test`. Status: open.
- **F-7: The 35% coverage gate fails narrow runs.** Single-file runs, narrow filters and `rake test:deps` fail on coverage alone, and SimpleCov result merging (600s) makes the outcome depend on recent runs. This is existing policy in `test_helper.rb`. Status: needs Dewayne's decision (keep it, enforce it only on full runs, or drop it).
- **F-8: No test CI.** The only workflow deploys docs on `docs/**` pushes to main and develop. Status: PR'd ([#8](https://github.com/MadBomber/kbs/pull/8)): Ruby 3.2 to 4.0 matrix plus a Redis service container, so CI runs the 22 Redis tests too; all 4 legs green.

### 2026-11-07 (no audit; backlog item F-2 worked; round-3 review items from #7)

- **F-9: `docs/advanced/performance.md` benchmark example uses `redis://localhost:6379/15` labeled "Test database".** After #7 the test suite no longer treats that database as disposable, so the docs contradict it. Fix: point the example at a throwaway server or say it flushes the target. Docs-site page, so it needs a before/after mkdocs screenshot. Status: open.
- **F-10: The throwaway-Redis recipe in `test/README.md` (from #7) does not check that port 6399 is free.** If another Redis already holds 6399, the daemonized start fails quietly, and the tests flush DB 15 of that server and `shutdown nosave` stops it. Fix: add a `redis-cli -p 6399 ping` pre-check line, or a note. Status: open (small; depends on #7 merging).
- **F-11: An opted-in Redis run against a down server reports each failure twice.** `teardown` calls `flushdb` again after `setup` failed on it. Only seen with a misconfigured URL. Status: open (low value; rejected in #7 review as noise).

### 2026-11-07, second run (no audit; backlog item F-8 worked)

- **F-12: The gemspec floor `>= 3.2.0` names a Ruby that went EOL on 2026-03-31.** Raised in [#8](https://github.com/MadBomber/kbs/pull/8) review round 2. The CI matrix follows the gemspec (3.2 to 4.0, all green), so nothing is broken today. Status: needs Dewayne's decision (keep 3.2, or raise the floor and the matrix together).
