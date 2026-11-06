# Findings: ragdoll

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | never | |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: (none)

## Findings

### 2026-11-05: found while fixing the develop suite (no audit run)

- **F-1** open. `ImageDescriptionService` (`image_description_service.rb:148`) and `ImageToTextService` (`image_to_text_service.rb:128`) call `ragdoll_config.ruby_llm_config`, which `Ragdoll::Core::Config` no longer defines after the `anyway_config` migration. The NoMethodError is rescued and logged, so both silently fall back to the ENV Ollama endpoint. Needs the right config key for the Ollama endpoint; see the FIXME at line 147.
- **F-2** open. `Database.disconnect!` calls `ActiveRecord::Base.clear_all_connections!`, which ActiveRecord 8.1 removed (`connection_handler.clear_all_connections!` exists). Raises NoMethodError. Small obvious-yes PR candidate.
- **F-3** needs Dewayne's decision. `SearchEngineTest#test_search_similar_content_returns_empty_results_when_embedding_is_nil` (test line 92) expects `[]`; `search_similar_content` returns `{results: [], statistics: nil, execution_time_ms: …}`. Fails only with a database. Which return shape is the contract?
- **F-4** needs Dewayne's decision. `Database.connected?` (and `Client#healthy?`, `client.rb:515`) reports whether a connection is checked out, not whether the database is reachable, so it is false right after setup. Change the meaning or document it.
- **F-5** needs Dewayne's decision. `EmbeddingService#generate_fallback_embedding` seeds from the service's `object_id`, so every fallback vector from one service is identical and similarity among them is 1.0.
- **F-6** needs Dewayne's decision. With no client, the outer rescue in `generate_embeddings_batch` swallows real Ollama errors and returns fallback vectors with only a printed warning.
- **F-7** open. That warning (`embedding_service.rb:197`) is a `puts` in library code, writing to the host app's stdout.
- **F-8** open. Several test files (`test/core/client_test.rb`, `test/core/core_test.rb`) use `return if ci_environment?`, which counts as a pass in CI instead of a skip; `search_tracking_integration_test.rb:36` redefines `ci_environment?`.
- **F-9** open. `ragdoll.gemspec` declares `activerecord` with no version; `ConnectionNotDefined` (used by the test hook) needs >= 7.1, currently guaranteed only through `neighbor`.
