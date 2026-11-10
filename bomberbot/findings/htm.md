# Findings: htm

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-07 | first run: bundle-audit (only S-1), read of every shell-out, eval/const_get, deserialization and file read; two low items held locally |
| dead code | 2026-11-07 | first full run: debride over lib and bin, tracked-file sweep, hand-checked each candidate |
| dependencies | 2026-11-06 | first run: Ruby 4.1/4.0.9 boot, bundle outdated, bundle-audit, gemspec constraints |
| adoption | 2026-11-10 | first run: README/API accuracy (clean), mkdocs `--strict` build, CHANGELOG vs released tags, gemspec metadata, llms.txt check |
| quality | 2026-11-07 | first run (third run today): rubocop over lib (only F-6), `rake flog_check` (nothing over 50; top is `RelevanceScorer#calculate_relevance` at 49.1), flay over lib; concrete local duplication only |

Dead-code scan last covered commit: cf7ec39 (2026-11-07 third run; 8d3a03f..cf7ec39 is #16's SQL and tests only, no new methods)

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
- **F-14: `FileSource#soft_delete_chunks!` does not cascade.** `htm.unload_file` runs `nodes_dataset.update(deleted_at: ...)`, which skips `Node#soft_delete!`, so the chunks' `node_tags` and `robot_nodes` rows stay active. Any query that trusts `node_tags.deleted_at` alone sees unloaded chunks. Status: merged ([#17](https://github.com/MadBomber/htm/pull/17), 2026-11-07).
- **F-15: `add_tag` does not revive a soft-deleted link.** `NodeTag.find_or_create` cannot see a soft-deleted row, so its INSERT hits the unique index, which is rescued and ignored, and the link stays deleted. Re-adding a tag to a node whose link was soft-deleted does nothing. Status: issue #19 (verified 2026-11-08; `Node#add_tags` also raises `Sequel::UniqueConstraintViolation` in the same case).
- **F-16: S-2, security finding, details held locally.** Low. Status: open.
- **F-17: S-3, security finding, details held locally.** Low, dev-only rake task. Status: open.
- Note for the next lane: `rake test` hardcodes `localhost:5432` in `set_test_env`, so a throwaway DB on another port has to be run with `ruby -Ilib -Itest -rtest_helper` over the same file list.

### 2026-11-07, third run (quality audit, first run, develop cf7ec39)

F-14 is PR'd in [#17](https://github.com/MadBomber/htm/pull/17). F-6 is still open and unchanged (the only rubocop offense in `lib`). F-7 is still open. F-13 and F-15 are still unverified. Flog shows nothing over 50, and the worst methods are 40 to 49 points with no single obvious split, so none of them is listed. Items 21 to 24 came from `/code-review` on #17. They are existing behaviour outside that diff.

- **F-18: the working-memory eviction block is duplicated.** `HTM#store_in_working_memory` (`lib/htm.rb:563`) and `HTM#add_to_working_memory` (`lib/htm.rb:616`) each run the same `has_space?` / `evict_to_make_space` / `mark_evicted` sequence (flay: IDENTICAL, mass 108). Extracting a private `make_working_memory_space(token_count)` is a small, local refactor. Status: open.
- **F-19: `check_database_config!` is defined twice.** The bodies in `lib/htm/mcp/cli.rb:140` and `lib/htm/mcp/server.rb:30` are identical (flay mass 76). Both are live: `bin/htm_mcp` calls `Server.start`, and the CLI calls its own copy. One shared module method would remove the copy. Status: open (check how `bin/htm_mcp` loads the CLI before moving it).
- **F-20: manual-tag linking re-implements `Node#add_tags`.** `HTM#enqueue_tags_job` (`lib/htm.rb:587`) and `RememberWorkflow#generate_tags_step` (`lib/htm/workflows/remember_workflow.rb:142`) both loop `Tag.find_or_create_with_ancestors` + `NodeTag.find_or_create`, which is exactly `Node#add_tags` (`lib/htm/models/node.rb:276`), and `handle_existing_node_tags` already uses it. Status: open (a small change; the htm.rb copy keeps a node lookup off the hot path, so measure or pass the id through a class-level helper).
- **F-21: `unload_file` leaves unloaded chunks in working memory.** `forget` calls `@working_memory.remove(node_id)`, and `unload_file` doesn't. So the HTM instance keeps serving those chunks in context until they are evicted. Status: PR'd ([#24](https://github.com/MadBomber/htm/pull/24), captures chunk ids before `source.unload!` then removes each from working memory; resolves issue #18).
- **F-22: `Node#restore!` revives every link, including ones that were deleted on their own.** It clears `deleted_at` on all of the node's `node_tags` and `robot_nodes`, so a tag removed by `rake htm:tags:cleanup` (or a soft-deleted robot link) comes back when the same content is remembered again. Matching on the node's own `deleted_at` timestamp (the cascade stamps one `now`) would restore only the rows from that deletion. Status: unverified. It touches restore semantics, so it may need Dewayne's call.
- **F-23: `unload_file` deletes the source outside the cascade transaction.** `soft_delete_chunks!` commits, then `source.delete` runs on its own. If the delete fails, the chunks are gone but the FileSource survives, and a later `load_file` without `force` skips the file as unchanged. Wrapping both in one `HTM.db.transaction` fixes it. Status: PR'd ([#20](https://github.com/MadBomber/htm/pull/20), via a new `FileSource#unload!`).
- **F-24: `MarkdownLoader#update_existing_chunk` revives a chunk with a bare `deleted_at: nil` update.** It doesn't use `Node#restore!`, so a chunk that `soft_delete_removed_chunks` deleted and that later reappears in the file comes back without its tag and robot links. Related to F-15. Status: unverified.

### 2026-11-08 (no audit; triage reproductions and builder review on #20, develop bf75a9f)

No audit ran: F-6, F-18, F-19, F-20 and F-23 were verified open, and the latest audit was a day old. Triage confirmed all five at bf75a9f and reproduced F-15 and F-21 on a throwaway DB. F-23 is PR'd in [#20](https://github.com/MadBomber/htm/pull/20). Items 25 to 28 came from the builder's review on #20.

- **F-25: `rake htm:files:unload` reports success for unknown files.** `unload_file` returns 0 when no source matches, and the task (`lib/tasks/files.rake`, about line 200) branches on `if result`, which is always truthy. Status: PR'd ([#25](https://github.com/MadBomber/htm/pull/25), branches on `result.positive?` and adds the repo's first rake-task test; resolves issue #21). Supersedes the orphan branch `bomber/rake-unload-unknown-file`.
- **F-26: deleting a FileSource nulls `source_id` on its soft-deleted chunks** (`ON DELETE SET NULL`), so they lose their link to the file. Reloading restores them only through `remember`'s content-hash dedup. Check that against the file-loading guide's claim that loading again restores them. Status: unverified.
- **F-27: `FileSource#unload!` and `load_file` take no row lock**, so a concurrent load and unload of one path can interleave (foreign-key error or orphaned nodes). Status: unverified, low.
- **F-28: `soft_delete_chunks!` round-trips chunk ids through Ruby** as three `IN (...)` lists where a subquery would do. It also skips chunks that were already soft-deleted, so their `node_tags` and `robot_nodes` stay active (related to F-15, F-24). Status: open (small).
- Also noted: `MarkdownLoader#load_file` (`markdown_loader.rb:56`) and `files.rake:144` build the FileSource lookup by hand instead of using the `by_path` scope. It would fit with F-18-style cleanup.

### 2026-11-10 (adoption audit, first run, develop 8c13a3f)

Adoption had never been run; it was the most overdue. README core API (`HTM.new(robot_name:)`, `remember`, `recall(topic, timeframe:)`, `RobotGroup.new(name:, active:, passive:)`, `failover!`), the "23 MCP tools" claim (11 `TOOLS` + 12 `GROUP_TOOLS`), `htm_mcp` subcommands, and every `ruby -c` on the example scripts all checked out clean. #22's version-banner fix is sound. F-6, F-18, F-19, F-20, F-28 re-verified open this visit; F-9 re-confirmed unreachable (still needs Dewayne's decision, tied to F-4/#13). No audit beyond adoption ran; dead-code scan last-covered commit stays cf7ec39.

- **F-29: no `docs/llms.txt` for AI discoverability.** `find -iname '*llms*'` → nothing. Status: PR'd ([#23](https://github.com/MadBomber/htm/pull/23), hand-authored 35-line llms.txt, every fact verified against lib/; docs-deploy change so not self-merged).
- **F-30: `mkdocs build --strict` fails on a dangling database-doc link.** `docs/database/README.md:8` links `public.node_relationships.md`, which is absent, though `node_relationships` is a live table (`db/schema.sql:122`, migration 00008) — the tbls docs were never regenerated. Status: issue [#26](https://github.com/MadBomber/htm/issues/26) (filed with F-31).
- **F-31: orphaned `docs/database/` pages for tables/views that no longer exist.** `db/schema.sql` has exactly 8 tables and 0 views, but `docs/database/` still ships `public.node_stats.md`, `public.nodes_tags.md`, `public.operations_log.md`, `public.relationships.md`, `public.robot_activity.md`, `public.topic_relationships.md`, `public.ontology_structure.md`. The `ontology_structure` case overlaps F-10: `lib/htm/long_term_memory/tag_operations.rb:99` still runs `SELECT * FROM ontology_structure`, a view nothing creates — a real code gap, not just docs. Status: the docs-regeneration half is issue [#26](https://github.com/MadBomber/htm/issues/26); the view-vs-remove-code half is needs Dewayne's decision (inbox, with F-10).
- **F-32: CHANGELOG missing sections for released versions up to 0.0.32.** `lib/htm/version.rb` = 0.0.32 with tags `v0.0.30`–`v0.0.32`, but the newest dated CHANGELOG section is `## [0.0.20] - 2025-12-22`; 0.0.21/0.0.30/0.0.31/0.0.32 sit under `## [Unreleased]`. The tag→commit mapping is provable from git, but which `[Unreleased]` entries belong to which release is a judgment call. Status: needs Dewayne's decision (inbox).
- **F-33: gemspec missing `documentation_uri`.** `htm.gemspec` sets homepage/source/changelog URIs and `rubygems_mfa_required` but no `documentation_uri`, though a docs site is published at `https://madbomber.github.io/htm`. Status: open (small; a one-line gemspec add, next-visit candidate).
