# Findings: kbs

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-08 | First run, develop at 27b1689. `bundle-audit check` against a lock resolved in scratch: no vulnerabilities (sqlite3 2.10.2, redis 5.4.1). No Marshal/YAML/shell-outs in lib; JSON.parse only; SQL bound except one raw-WHERE API. Findings: F-26, S-2 to S-4. |
| dead code | 2026-11-07 | Quick scan only (debride over lib/ at 7381f82): every candidate is public API (attr readers, DSL helpers, Blackboard aliases), so nothing removable without a deprecation decision. Not the full audit. |
| dependencies | 2026-11-06 | First run. `bundle outdated`: only major bumps are available (minitest 6, simplecov 1.x, redis 6), so none was taken. Ruby 4 readiness greps were clean. Suite green on 4.1.0-preview1 and 4.0.9. |
| adoption | 2026-11-12 | First run, develop at cff8e89. `mkdocs build --strict` clean; README blocks 01-04 run; 20 of 26 example runs completed, 4 csv examples fail under bundle exec on Ruby >= 3.4, 2 AI examples need Ollama; no llms.txt; CHANGELOG missing 0.2.0; gem ships docs/ and plumbing. Findings: F-28 to F-37. |
| quality | 2026-11-13 | First run, develop at 2dc666f. Default-config rubocop (repo has none) 469 offenses (227 EmptyLineBetweenDefs); flog 1863 total, 7.2/method avg (worst `Decompiler#decompile_body` 131); flay 1289 (JoinNode/NegationNode and Fact/Blackboard::Fact exact dups). Reproduced duplicate firing after a late `add_rule`, ignored rule priority, `matches` helper shadowing. No newly unused private code 27b1689..2dc666f. Findings: F-41 to F-49. |

Dead-code scan last covered commit: 2dc666f (2026-11-13; quality auditor checked 27b1689..2dc666f: the six new private Decompiler methods and `NegationNode#left_deactivate`/`#reset` all have callers, nothing newly unused. Earlier: 27b1689 (2026-11-08; the improver scanned c9a2948..27b1689: only #13 touched lib/, and the private methods it removed have no remaining callers; nothing new to remove)

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

- **F-15: `KBS.knowledge_base(engine:)` raises ArgumentError, yet the README and docs call it 22 times.** `lib/kbs/dsl.rb:15` takes no arguments and `lib/kbs/dsl/knowledge_base.rb:8` hard-codes `Engine.new`. The README "Blackboard with SQLite Persistence" block fails on its first call. Fix: an optional `engine:` keyword defaulting to nil. Status: issue [#10](https://github.com/MadBomber/kbs/issues/10); PR open (see repos/kbs.md).
- **F-16: A rule whose first condition is `without` never fires.** `NegationNode` only emits on `left_activate`, and the root dummy token predates the node. Documented at `docs/guides/dsl.md:744-747`. Status: issue [#11](https://github.com/MadBomber/kbs/issues/11), closed; fixed on `main` only by Copilot [#23](https://github.com/MadBomber/kbs/pull/23) (2026-11-08); reaches `develop` with the main-into-develop sync.
- **F-17: README Testing and Documentation sections are stale.** Nonexistent test files, a "100% with 199 tests" claim, `KBS::ReteEngine`, an incomplete helper list, and a wrong example filename in CHANGELOG.md:14. Status: issue [#12](https://github.com/MadBomber/kbs/issues/12).
- **F-18: Shared `?variables` don't constrain joins.** `Engine#build_network_for_rule` only builds a `:type` join test, so `on :order, id: :oid?; on :item, order_id: :oid?` fires on non-matching ids. `docs/guides/variable-binding.md` promises join constraints; `git log -S variable_bindings` shows it was never built. Fixing it changes which rules fire. Status: needs Dewayne's decision (implement joins, document bind-only, or opt-in).
- **F-19: The documented predicate query `query(:temperature, value: greater_than(80))` (`docs/what-is-a-rule.md:508`) doesn't work.** `greater_than` is undefined in `KnowledgeBase`, and `query` compares with `==` only, so a lambda returns `[]`. Status: needs Dewayne's decision (teach `query` predicates, adding API, or fix the doc example).
- **F-20: `Fact#matches?` rejects `false`/`nil` attribute values before calling a Proc** (`lib/kbs/fact.rb:26`), so `satisfies { |v| v == false }` can never match. Status: needs Dewayne's decision (behavior change for predicate rules).
- **F-21: `BlackboardMemory`, `BlackboardEngine` and `PersistedFact` are commented "deprecated - will be removed in v1.0" but never warn** (`lib/kbs/blackboard.rb:18-23`). Status: needs Dewayne's decision (warn now or leave until 1.0).
- **F-22: `examples/temp_dsl.txt` is 273 KB of captured example output, referenced nowhere, and ships in the gem** (largest file in the package). Status: issue [#15](https://github.com/MadBomber/kbs/issues/15), closed; fixed on `main` only by Copilot [#26](https://github.com/MadBomber/kbs/pull/26); reaches `develop` with the sync.
- **F-23: Bindings after a `without` come from the wrong fact.** `Rule#extract_bindings` and `Blackboard::Engine#extract_bindings_from_token` index `Token#facts` by the all-conditions index, but negations add no fact. Status: PR'd ([#13](https://github.com/MadBomber/kbs/pull/13)); adds public `Rule#bindings_for`, shared by `Rule#fire` and the blackboard audit log.
- **F-24: Join tests after a `without` index the wrong fact.** `Engine#build_network_for_rule` sets `token_field_index` from the all-conditions position, but `token.facts` skips negations. Same mismatch as F-23, inside the F-18 join work; noticed by the #13 builder. Status: open (part of F-18).
- **F-25: `Rule#fire` passes two arguments to a zero-argument lambda action, which raises ArgumentError.** Noticed by the #13 builder. Reproduced 2026-11-08: `-> {}` and a zero-arity `Method` raise `ArgumentError (given 2, expected 0)`; procs are fine, so DSL `perform` blocks are unaffected. Status: issue [#14](https://github.com/MadBomber/kbs/issues/14), closed; fixed on `main` only by Copilot [#24](https://github.com/MadBomber/kbs/pull/24); reaches `develop` with the sync.
- **S-1: security finding, details held locally.** Status: open.

### 2026-11-08, second visit (security audit, develop at 27b1689)

- **F-26: `RedisStore#query_facts` ignores its conditions and returns every active fact.** `redis_store.rb:119-124` drops `conditions` and `params`; `HybridStore#query_facts` delegates to it; `docs/api/blackboard.md:279-302` says Redis raises NotImplementedError. Found by code reading. Status: issue [#16](https://github.com/MadBomber/kbs/issues/16), closed; fixed on `main` only by Copilot [#27](https://github.com/MadBomber/kbs/pull/27) (now raises); reaches `develop` with the sync.
- **F-27: `Decompiler` emits `/* getlocal_WC_1 */` for values captured by the condition helpers.** `lib/kbs/decompiler.rb:65` handles `getlocal_WC_0` only, so `rule_source` for a rule without a source file prints `->(v) { v > /* getlocal_WC_1 */ }` for `greater_than(80)`. Reproduced by the responder. Relevant to issue #3 (`to_s` in DSL form). Status: PR'd ([#33](https://github.com/MadBomber/kbs/pull/33)).
- **S-2: security finding, details held locally.** Status: open.
- **S-3: security finding, details held locally.** Status: duplicate of S-1.
- **S-4: security finding, details held locally.** Status: open.

### 2026-11-12 (adoption audit, develop at cff8e89; resumed visit)

- **F-28: The documented DSL form `rule "name", priority: N do` raises ArgumentError.** `lib/kbs/dsl/knowledge_base.rb:16` is `def rule(name, &block)`; the docs use `priority:` 57 times across 9 pages, including the first Quick Start example. `RuleBuilder#priority` already exists. Fix: a `priority:` keyword. Not sync-safe (file diverged). Status: issue [#29](https://github.com/MadBomber/kbs/issues/29); PR'd ([#37](https://github.com/MadBomber/kbs/pull/37), 2026-11-13).
- **F-29: The README AI integration example never configures Ollama**, so `RubyLLM::Chat.new(provider: :ollama)` raises; the model is hard-coded despite an `OLLAMA_MODEL` comment; `ruby_llm` install isn't mentioned. Not sync-safe (README diverged). Status: issue [#30](https://github.com/MadBomber/kbs/issues/30); merged ([#38](https://github.com/MadBomber/kbs/pull/38), low risk, 2026-11-13; LM Studio per Dewayne's comment on #30).
- **F-30: Four CSV examples fail under `bundle exec` on Ruby >= 3.4** (`csv` is a bundled gem, not in the Gemfile). Status: merged ([#32](https://github.com/MadBomber/kbs/pull/32), 2026-11-12).
- **F-31: CHANGELOG has no 0.2.0 entry; the 0.2.1 entry describes 0.2.0** (tags v0.2.0 2026-02-26, v0.2.1 2026-02-27 only drops Gemfile.lock). Not sync-safe (CHANGELOG diverged). Status: open (after the sync).
- **F-32: No `llms.txt`.** `docs/**` deploys the site, so not low risk. Status: open.
- **F-33: The gem ships docs/, examples and repo plumbing** (`git ls-files` minus four rejects; 332 KB). Fix: a `spec.files` allowlist plus a packaged-file test. Status: issue [#31](https://github.com/MadBomber/kbs/issues/31); PR'd ([#39](https://github.com/MadBomber/kbs/pull/39), 2026-11-13; package 314 KB to 31 KB).
- **F-34: Gemspec description claims native Ollama/OpenAI integration and session management that lib lacks; no `documentation_uri`/`bug_tracker_uri`; `changelog_uri` points at main.** Sync-safe. Status: open (small PR candidate).
- **F-35: `docs/installation.md` says Ruby 2.7+ and sqlite3 optional**; the gemspec says >= 3.2 and sqlite3 is a runtime dependency. Docs-deploy file. Status: open.
- **F-36: The Pages workflow deploys on `docs/**` pushes to both `main` and `develop` while their docs differ**, so the live site shows whichever branch pushed last. Status: needs Dewayne's decision (deploy from one branch, or accept until the sync).
- **F-37: `main` brought back the Redis test fallback to `redis://localhost:6379/15` with `flushdb`** (Copilot #27 era `test/test_redis_store.rb:8`, `test/test_hybrid_store.rb:8`; F-2 regressed on main only). Running main's tests directly flushed DB 15 of the local Redis during the 2026-11-12 triage. Also `main` has no Test workflow, so Copilot #19-#27 merged untested. Status: PR'd ([#28](https://github.com/MadBomber/kbs/pull/28) keeps develop's opt-in versions).
- **F-38: `Decompiler` renders `a && b` as `a; b`**, so `between(1, 9)` reads `->(v) { v >= 1; v <= 9 }` and drops the conjunction. Noticed by the #33 builder. Status: issue [#36](https://github.com/MadBomber/kbs/issues/36) (2026-11-13).
- **F-39: `Rule#to_s` (from main's #21) raises on a condition lambda the decompiler can't handle** (only the action decompile is rescued), and it duplicates `KnowledgeBase#reconstruct_rule_source` (omits `desc`). Noticed by the #28 builder. Status: open (after #28).
- **F-40: `HybridStore#query_facts` delegates to `RedisStore`, so its `NotImplementedError` names `RedisStore`;** deleting both overrides lets the `Store` base raise apply. Noticed by the #28 builder. Status: open (after #28).

### 2026-11-13 (quality audit, develop at 2dc666f)

- **F-41: A rule added after facts exist makes earlier rules fire twice.** `Engine#build_network_for_rule` (`engine.rb:148-152`) replays every fact into every alpha memory, and `AlphaMemory#activate` doesn't skip duplicates. Same in `Blackboard::Engine` (duplicate audit entries). Reproduced. Status: issue [#34](https://github.com/MadBomber/kbs/issues/34).
- **F-42: `priority` has no effect on firing order.** `Engine#run` and `Blackboard::Engine#run` walk `@production_nodes` in insertion order; docs say higher fires first. Reproduced. `docs/api/rules.md:154-155` and `docs/advanced/testing.md:378-399` are inconsistent with each other. Status: issue [#35](https://github.com/MadBomber/kbs/issues/35).
- **F-43: Inside a `rule` block, `matches(/re/)` silently breaks the rule.** `RuleBuilder#matches` (alias of `on`, `rule_builder.rb:63`) shadows `ConditionHelpers#matches`; docs use the helper form at `docs/guides/dsl.md:624,627`. Status: needs Dewayne's decision (make `matches` dispatch on a lone Regexp/String, or drop the alias).
- **F-44: `RedisStore#transaction` is a no-op with identical branches** (`redis_store.rb:183-196`); Memory's writes are not atomic on Redis; `HybridStore#transaction` same. Status: open (the plain-`yield` cleanup is a small PR; real atomicity needs Dewayne's decision).
- **F-45: `Blackboard::Engine#update` duplicates the inherited `KBS::Engine#update`** (flay mass 128). Status: open (pure deletion PR candidate).
- **F-46: Exact duplicates across classes:** `JoinNode`/`NegationNode#perform_join_tests`, `KBS::Fact`/`Blackboard::Fact#matches?`, `matches_pattern?` in Redis/SQLite stores. Status: open (extract shared modules; F-20/F-24 then get fixed once).
- **F-47: `RedisStore#get_facts` has broken if/else indentation** (`redis_store.rb:93-97`). Status: open (fold into F-44 or F-46).
- **F-48: Write-only ivars:** `Rule@fired_count` (`rule.rb:13,19`; docs/api/rules.md:344 mentions it) and `RuleBuilder@current_condition_group` (`rule_builder.rb:16`). Status: open for `@current_condition_group`; `@fired_count` needs Dewayne's decision (remove or expose `attr_reader`).
- **F-49: Minor DSL hygiene:** `PatternEvaluator#method_missing` lacks `respond_to_missing?`; `RuleBuilder#then` shadows `Kernel#then`; `any()` has an unused block arg. Status: open (low value).
- **F-50: The gem's `homepage_uri` and `source_code_uri` are identical, so `gem build` warns.** Noticed by the #39 builder. Status: open (rides with F-34 metadata).
