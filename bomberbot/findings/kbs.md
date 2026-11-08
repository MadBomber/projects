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

Dead-code scan last covered commit: c9a2948 (2026-11-08; the improver read lib/ at c9a2948: nothing non-public is unused in lib/; `examples/temp_dsl.txt` is F-22)

## Findings

### 2026-11-06 (dependencies audit)

- **F-1: `rake test` ran zero tests.** `Minitest::TestTask` loads `minitest/autorun` before `test_helper` starts SimpleCov, so SimpleCov's at_exit hook fails the 35% gate and the run is skipped. Status: merged ([#6](https://github.com/MadBomber/kbs/pull/6), 2026-11-07).
- **F-2: Redis tests flush DB 15 of whatever Redis is on `localhost:6379`.** The URL is hard-coded in `test/test_redis_store.rb` and `test/test_hybrid_store.rb`. Fix: read the URL from an env var such as `KBS_TEST_REDIS_URL`, and skip the tests when it is unset. Status: merged ([#7](https://github.com/MadBomber/kbs/pull/7), 2026-11-07); the tests now skip unless `KBS_TEST_REDIS_URL` is set.
- **F-3: `redis_available?` only rescues LoadError, CannotConnectError and TimeoutError.** A Redis that needs AUTH, or that rejects DB 15, makes setup raise errors instead of skipping. Status: merged ([#7](https://github.com/MadBomber/kbs/pull/7), 2026-11-07); with the opt-in, no local Redis is touched unless named, and an opted-in bad URL errors loudly by design.
- **F-4: `require "kbs/blackboard"` hard-requires the `redis` gem.** The require chain is `blackboard.rb` to `persistence/redis_store.rb` to `require 'redis'`, but `redis` is only a development dependency in the gemspec, so a user without the redis gem gets a LoadError even when using only SQLite. Seen in code; not yet reproduced in a gem environment without redis. Fix options: lazy-require redis inside the Redis classes, or make redis a runtime dependency. Status: merged ([#9](https://github.com/MadBomber/kbs/pull/9), 2026-11-07). Verified 2026-11-07: with the gem built from develop in an empty `GEM_HOME` (only kbs and sqlite3), `require "kbs/blackboard"` raised `LoadError: cannot load such file -- redis`. #9 soft-requires redis at load time and requires it again in `RedisStore#connect`.
- **F-5: Unused Gemfile entries.** Nothing in the repo requires `aigcm` or `debug_me` (grep over all `.rb` files). Meanwhile `examples/ai_enhanced_kbs*.rb` require `ruby_llm`, which the Gemfile doesn't list. Status: open (development-only, not public API). Since [#8](https://github.com/MadBomber/kbs/pull/8), every CI leg installs `aigcm` and its `ruby_llm` tree too, so this now costs CI time as well.
- **F-6: test/README.md drift.** It documents a hand-written `ruby -Ilib:test -e ...` loader and an 80%/70% coverage gate, while `test_helper.rb` enforces 35% with no per-file gate. README.md and docs/installation.md say `rake test`. Status: open.
- **F-7: The 35% coverage gate fails narrow runs.** Single-file runs, narrow filters and `rake test:deps` fail on coverage alone, and SimpleCov result merging (600s) makes the outcome depend on recent runs. This is existing policy in `test_helper.rb`. Status: needs Dewayne's decision (keep it, enforce it only on full runs, or drop it).
- **F-8: No test CI.** The only workflow deploys docs on `docs/**` pushes to main and develop. Status: merged ([#8](https://github.com/MadBomber/kbs/pull/8), 2026-11-07): Ruby 3.2 to 4.0 matrix plus a Redis service container, so CI runs the 22 Redis tests too; all 4 legs green.

### 2026-11-07 (no audit; backlog item F-2 worked; round-3 review items from #7)

- **F-9: `docs/advanced/performance.md` benchmark example uses `redis://localhost:6379/15` labeled "Test database".** After #7 the test suite no longer treats that database as disposable, so the docs contradict it. Fix: point the example at a throwaway server or say it flushes the target. Docs-site page, so it needs a before/after mkdocs screenshot. Status: open.
- **F-10: The throwaway-Redis recipe in `test/README.md` (from #7) does not check that port 6399 is free.** If another Redis already holds 6399, the daemonized start fails quietly, and the tests flush DB 15 of that server and `shutdown nosave` stops it. Fix: add a `redis-cli -p 6399 ping` pre-check line, or a note. Status: open (small; depends on #7 merging).
- **F-11: An opted-in Redis run against a down server reports each failure twice.** `teardown` calls `flushdb` again after `setup` failed on it. Only seen with a misconfigured URL. Status: open (low value; rejected in #7 review as noise).

### 2026-11-07, second run (no audit; backlog item F-8 worked)

- **F-12: The gemspec floor `>= 3.2.0` names a Ruby that went EOL on 2026-03-31.** Raised in [#8](https://github.com/MadBomber/kbs/pull/8) review round 2. The CI matrix follows the gemspec (3.2 to 4.0, all green), so nothing is broken today. Status: needs Dewayne's decision (keep 3.2, or raise the floor and the matrix together).

### 2026-11-07, third run (no audit; finding F-4 worked)

- **F-13: A broken redis install is silent at require time after [#9](https://github.com/MadBomber/kbs/pull/9).** The load-time `rescue LoadError` also swallows a LoadError from inside an installed redis (for example a missing redis-client). `RedisStore#connect` re-raises the real cause, but app code that calls `Redis.new` itself sees `NameError: uninitialized constant Redis`. Raised in #9 review round 2 and rejected as an edge case. Status: open (low value).
- **F-14: test/README.md "No Mocking Policy" headline says "no mocks or stubs" above a list of two exceptions.** Raised in #9 review round 3. Status: open (doc nit).

### 2026-11-08 (no audit; triage and improver ran; backlog empty)

- **F-15: `KBS.knowledge_base(engine:)` raises ArgumentError, yet the README and docs call it 22 times.** `lib/kbs/dsl.rb:15` takes no arguments and `lib/kbs/dsl/knowledge_base.rb:8` hard-codes `Engine.new`. The README "Blackboard with SQLite Persistence" block fails on its first call. Fix: an optional `engine:` keyword defaulting to nil. Status: issue [#10](https://github.com/MadBomber/kbs/issues/10).
- **F-16: A rule whose first condition is `without` never fires.** `NegationNode` only emits on `left_activate`, and the root dummy token predates the node. Documented at `docs/guides/dsl.md:744-747`. Status: issue [#11](https://github.com/MadBomber/kbs/issues/11).
- **F-17: README Testing and Documentation sections are stale.** Nonexistent test files, a "100% with 199 tests" claim, `KBS::ReteEngine`, an incomplete helper list, and a wrong example filename in CHANGELOG.md:14. Status: issue [#12](https://github.com/MadBomber/kbs/issues/12).
- **F-18: Shared `?variables` don't constrain joins.** `Engine#build_network_for_rule` only builds a `:type` join test, so `on :order, id: :oid?; on :item, order_id: :oid?` fires on non-matching ids. `docs/guides/variable-binding.md` promises join constraints; `git log -S variable_bindings` shows it was never built. Fixing it changes which rules fire. Status: needs Dewayne's decision (implement joins, document bind-only, or opt-in).
- **F-19: The documented predicate query `query(:temperature, value: greater_than(80))` (`docs/what-is-a-rule.md:508`) doesn't work.** `greater_than` is undefined in `KnowledgeBase`, and `query` compares with `==` only, so a lambda returns `[]`. Status: needs Dewayne's decision (teach `query` predicates, adding API, or fix the doc example).
- **F-20: `Fact#matches?` rejects `false`/`nil` attribute values before calling a Proc** (`lib/kbs/fact.rb:26`), so `satisfies { |v| v == false }` can never match. Status: needs Dewayne's decision (behavior change for predicate rules).
- **F-21: `BlackboardMemory`, `BlackboardEngine` and `PersistedFact` are commented "deprecated - will be removed in v1.0" but never warn** (`lib/kbs/blackboard.rb:18-23`). Status: needs Dewayne's decision (warn now or leave until 1.0).
- **F-22: `examples/temp_dsl.txt` is 273 KB of captured example output, referenced nowhere, and ships in the gem** (largest file in the package). Status: open (clean removal candidate for a later visit).
- **F-23: Bindings after a `without` come from the wrong fact.** `Rule#extract_bindings` and `Blackboard::Engine#extract_bindings_from_token` index `Token#facts` by the all-conditions index, but negations add no fact. Status: PR'd ([#13](https://github.com/MadBomber/kbs/pull/13)); adds public `Rule#bindings_for`, shared by `Rule#fire` and the blackboard audit log.
- **F-24: Join tests after a `without` index the wrong fact.** `Engine#build_network_for_rule` sets `token_field_index` from the all-conditions position, but `token.facts` skips negations. Same mismatch as F-23, inside the F-18 join work; noticed by the #13 builder. Status: open (part of F-18).
- **F-25: `Rule#fire` passes two arguments to a zero-argument lambda action, which raises ArgumentError.** Noticed by the #13 builder; not reproduced yet. Status: unverified.
- **S-1: security finding, details held locally.** Status: open.
