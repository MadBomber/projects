# Findings: htm

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-07 | first run: bundle-audit (only S-1), read of every shell-out, eval/const_get, deserialization and file read; two low items held locally |
| dead code | 2026-11-07 | first full run: debride over lib and bin, tracked-file sweep, hand-checked each candidate |
| dependencies | 2026-11-06 | first run: Ruby 4.1/4.0.9 boot, bundle outdated, bundle-audit, gemspec constraints |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: 8d3a03f (2026-11-07 second run; 6626985..8d3a03f only removed the .bak files, nothing new)

## Findings

### 2026-11-06 (dependencies audit, first bomberbot pass)

- **F-1: ConfigurationTest fails whenever `HTM_SERVICE__NAME` is set.** The Rakefile uses that variable to pick the test database, but five naming tests assume the default `htm`, so you couldn't run the suite green against a non-default test DB. Status: PR'd ([#14](https://github.com/MadBomber/htm/pull/14)).
- **F-2: does not install on Ruby 4.1.0-preview1.** `tiktoken_ruby` 0.0.17 (the latest) has no 4.1 binary, and the source build fails in `magnus` (`missing field handle_weak_references`). The fix is upstream; htm only needs tiktoken for the default `token_counter` (`lib/htm/config/builder.rb`) and the test helper. Status: needs Dewayne's decision on whether to make tiktoken optional with a fallback, or wait for upstream before Ruby 4.1 ships.
- **F-3: S-1, security finding, details held locally.** Status: needs Dewayne's decision (it involves a major dependency bump).
- **F-4: the hybrid search docstring contradicts the implementation, and issue #13 is still open.** The `HybridSearch` module header says "three independent searches", but since 4993548 (2026-02-12) the vector step only re-ranks fulltext and tag candidates. The docstring on `search_hybrid_rrf` documents this as a deliberate trade-off. Issue #13 asks for the independent-merge behaviour, which d96afdf delivered on 2025-12-30 before 4993548 reverted it. Status: needs Dewayne's decision. Either restore an independent vector CTE (`ORDER BY embedding <=> q LIMIT candidate_limit`, which can use the HNSW index) or keep rerank and fix the module docstring and docs.
- **F-5: dead files tracked in git:** `test/caching_test.rb.bak3` and `test/ontology_test.rb.bak`. Neither matches `*_test.rb`, so they never run. Status: PR'd ([#15](https://github.com/MadBomber/htm/pull/15)), which also adds `*.bak` patterns to `.gitignore`.
- **F-6: one rubocop offense on develop:** `lib/htm/long_term_memory/hybrid_search.rb:324`, `Layout/SpaceAroundOperators`, autocorrectable. No CI runs rubocop. Status: open (small).
- **F-7: no test CI.** The only workflow deploys docs, so PRs get no checks. The suite needs Postgres with pgvector and pg_trgm. Status: open (a workflow with a pgvector service container would be a candidate; check with Dewayne first).
- **F-8: `required_ruby_version >= 3.0.0` is probably stale given current dependency floors.** Status: unverified.

### 2026-11-07 (dead-code audit, first full run, up to 6626985)

debride over `lib` and `bin` flagged about 120 methods. Almost all of them are public API (`HTM`, `Config`, the model dataset scopes), Sequel hooks, or rake entry points, so none are dead. The items below are the ones that held up when I checked them by hand. F-1 is merged ([#14](https://github.com/MadBomber/htm/pull/14)). F-6 and F-7 are still open and unchanged.

- **F-9: the Ruby-side RRF merge path in `HybridSearch` is unreachable.** Since 4993548 (2026-02-12), `search_hybrid_rrf` does the fusion in one SQL CTE. These private helpers have no callers in `lib`: `fetch_vector_candidates`, `fetch_fulltext_candidates`, `fetch_tag_candidates`, `merge_with_rrf`, `merge_vector_rrf_entry`, `merge_fulltext_rrf_entry`, `merge_tag_rrf_entry` and `init_rrf_entry`. That is about 180 lines in `lib/htm/long_term_memory/hybrid_search.rb`. Only two `merge_with_rrf` tests in `test/hybrid_search_rrf_test.rb` reach them, via `send`. `build_tag_depth_map`, `calculate_tag_depth_score` and `parse_pg_array` are still live. Status: needs Dewayne's decision, tied to F-4. If he keeps the SQL rerank, removing these helpers and their two tests is an obvious-yes removal PR. If he restores independent vector search, `fetch_vector_candidates` may be worth reusing.
- **F-10: `LongTermMemory#ontology_structure` raises `PG::UndefinedTable`.** It queries an `ontology_structure` view that no migration and nothing in `db/schema.sql` creates. `HTM::Database`'s drop routine only drops it, along with a `topic_relationships` view and an `extract_ontology_topics()` function, neither of which the current schema has. I reproduced it on a freshly migrated throwaway DB. `docs/database/public.ontology_structure.md` (plus its SVG) and `docs/database_rake_tasks.md` still mention the view. Status: needs Dewayne's decision. The method is public, so the options are to add the view back in a migration, or to deprecate the method and remove the docs and stale drops.
- **F-11: `LongTermMemory#topic_relationships` has no running test.** The only tests for it were in `test/ontology_test.rb.bak`, which never ran. The method is plain SQL now, not a view, and works (verified against a throwaway DB). A test for its `min_shared_nodes` floor, its `MAX_TAG_QUERY_LIMIT` clamp, and the `t1.name < t2.name` dedup would be a small, obvious-yes test PR. Status: PR'd ([#16](https://github.com/MadBomber/htm/pull/16)), which also fixes F-12.

### 2026-11-07, second run (security audit, first run, develop 8d3a03f)

F-11 is PR'd in [#16](https://github.com/MadBomber/htm/pull/16). F-6 and F-7 are still open and unchanged.

- **F-12: `topic_relationships` counted soft-deleted data.** Its raw SQL ignored `deleted_at`, so memories removed with `forget` or `unload_file`, and soft-deleted tags, still added to `shared_nodes`. Found while writing the F-11 test. Status: PR'd ([#16](https://github.com/MadBomber/htm/pull/16)).
- **F-13: other tag queries also skip soft-delete filters.** `popular_tags` (`base_popular_tags_query` joins `node_tags` and `nodes` with no `deleted_at` checks), and `get_node_tags`, `node_topics` and `batch_load_node_tags` (they join the raw `node_tags` table through `Tag`, so only the tags scope applies). So forgotten memories still count toward popular tags, which also feed `TagService.extract` as the existing ontology. Status: unverified (found by `/code-review` on #16; needs a failing test like the ones in #16).
- **F-14: `FileSource#soft_delete_chunks!` does not cascade.** `htm.unload_file` runs `nodes_dataset.update(deleted_at: ...)`, which skips `Node#soft_delete!`, so the chunks' `node_tags` and `robot_nodes` rows stay active. Any query that trusts `node_tags.deleted_at` alone sees unloaded chunks. Status: open (a small fix: soft-delete each node, or cascade in one update per table, plus a test).
- **F-15: `add_tag` does not revive a soft-deleted link.** `NodeTag.find_or_create` cannot see a soft-deleted row, so its INSERT hits the unique index, which is rescued and ignored, and the link stays deleted. Re-adding a tag to a node whose link was soft-deleted does nothing. Status: unverified.
- **F-16: S-2, security finding, details held locally.** Low. Status: open.
- **F-17: S-3, security finding, details held locally.** Low, dev-only rake task. Status: open.
- Note for the next lane: `rake test` hardcodes `localhost:5432` in `set_test_env`, so a throwaway DB on another port has to be run with `ruby -Ilib -Itest -rtest_helper` over the same file list.
