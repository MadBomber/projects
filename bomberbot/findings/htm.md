# Findings: htm

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | 2026-11-06 | first run: Ruby 4.1/4.0.9 boot, bundle outdated, bundle-audit, gemspec constraints |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: aee26d2 (quick scan, 2026-11-06)

## Findings

### 2026-11-06 (dependencies audit, first bomberbot pass)

- **F-1: ConfigurationTest fails whenever `HTM_SERVICE__NAME` is set.** The Rakefile uses that variable to pick the test database, but five naming tests assume the default `htm`, so you couldn't run the suite green against a non-default test DB. Status: PR'd ([#14](https://github.com/MadBomber/htm/pull/14)).
- **F-2: does not install on Ruby 4.1.0-preview1.** `tiktoken_ruby` 0.0.17 (the latest) has no 4.1 binary, and the source build fails in `magnus` (`missing field handle_weak_references`). The fix is upstream; htm only needs tiktoken for the default `token_counter` (`lib/htm/config/builder.rb`) and the test helper. Status: needs Dewayne's decision on whether to make tiktoken optional with a fallback, or wait for upstream before Ruby 4.1 ships.
- **F-3: S-1, security finding, details held locally.** Status: needs Dewayne's decision (it involves a major dependency bump).
- **F-4: the hybrid search docstring contradicts the implementation, and issue #13 is still open.** The `HybridSearch` module header says "three independent searches", but since 4993548 (2026-02-12) the vector step only re-ranks fulltext and tag candidates. The docstring on `search_hybrid_rrf` documents this as a deliberate trade-off. Issue #13 asks for the independent-merge behaviour, which d96afdf delivered on 2025-12-30 before 4993548 reverted it. Status: needs Dewayne's decision. Either restore an independent vector CTE (`ORDER BY embedding <=> q LIMIT candidate_limit`, which can use the HNSW index) or keep rerank and fix the module docstring and docs.
- **F-5: dead files tracked in git:** `test/caching_test.rb.bak3` and `test/ontology_test.rb.bak`. Neither matches `*_test.rb`, so they never run. Status: open (an obvious-yes removal PR for a later run).
- **F-6: one rubocop offense on develop:** `lib/htm/long_term_memory/hybrid_search.rb:324`, `Layout/SpaceAroundOperators`, autocorrectable. No CI runs rubocop. Status: open (small).
- **F-7: no test CI.** The only workflow deploys docs, so PRs get no checks. The suite needs Postgres with pgvector and pg_trgm. Status: open (a workflow with a pgvector service container would be a candidate; check with Dewayne first).
- **F-8: `required_ruby_version >= 3.0.0` is probably stale given current dependency floors.** Status: unverified.
