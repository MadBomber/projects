# Findings: ragdoll

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | 2026-11-06 | bundle outdated, bundle-audit, gemspec constraints, Ruby 4 readiness (suite green on 4.0.9). Findings S-1, F-10 to F-12. |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: 19bde2b (2026-11-07 quick scan of 66a9237..19bde2b; nothing removable)

## Findings

### 2026-11-05: found while fixing the develop suite (no audit run)

- **F-1** open. `ImageDescriptionService` (`image_description_service.rb:148`) and `ImageToTextService` (`image_to_text_service.rb:128`) call `ragdoll_config.ruby_llm_config`, which `Ragdoll::Core::Config` no longer defines after the `anyway_config` migration. The NoMethodError is rescued and logged, so both silently fall back to the ENV Ollama endpoint. Needs the right config key for the Ollama endpoint; see the FIXME at line 147.
- **F-2** merged ([#6](https://github.com/MadBomber/ragdoll/pull/6), merged 2026-11-07). `Database.disconnect!` calls `ActiveRecord::Base.clear_all_connections!`, which ActiveRecord 8.1 removed (`connection_handler.clear_all_connections!` exists). Raises NoMethodError. Small obvious-yes PR candidate.
- **F-3** needs Dewayne's decision. `SearchEngineTest#test_search_similar_content_returns_empty_results_when_embedding_is_nil` (test line 92) expects `[]`; `search_similar_content` returns `{results: [], statistics: nil, execution_time_ms: …}`. Fails only with a database. Which return shape is the contract?
- **F-4** needs Dewayne's decision. `Database.connected?` (and `Client#healthy?`, `client.rb:515`) reports whether a connection is checked out, not whether the database is reachable, so it is false right after setup. Change the meaning or document it.
- **F-5** needs Dewayne's decision. `EmbeddingService#generate_fallback_embedding` seeds from the service's `object_id`, so every fallback vector from one service is identical and similarity among them is 1.0.
- **F-6** needs Dewayne's decision. With no client, the outer rescue in `generate_embeddings_batch` swallows real Ollama errors and returns fallback vectors with only a printed warning.
- **F-7** open. That warning (`embedding_service.rb:197`) is a `puts` in library code, writing to the host app's stdout.
- **F-8** open. Several test files (`test/core/client_test.rb`, `test/core/core_test.rb`) use `return if ci_environment?`, which counts as a pass in CI instead of a skip; `search_tracking_integration_test.rb:36` redefines `ci_environment?`.
- **F-9** open. `ragdoll.gemspec` declares `activerecord` with no version; `ConnectionNotDefined` (used by the test hook) needs >= 7.1, currently guaranteed only through `neighbor`.

### 2026-11-06: dependencies audit (develop 66a9237)

Status check of earlier findings on 66a9237: F-1, F-7, F-8, F-9 still present and open. F-3 still the only failure in the full database suite (1294 runs). The develop suite merged via #4 and #5 is green in CI.

- **S-1** part 1 PR'd ([#7](https://github.com/MadBomber/ragdoll/pull/7), 2026-11-07); remainder needs Dewayne's decision. Security finding, details held locally. Most of it is an obvious-yes lockfile update within existing constraints; one part needs a major dependency bump and Dewayne's decision.
- **F-10** open. CI matrix (`.github/workflows/ruby.yml`) tests Ruby 3.2, 3.3, 3.4 only. The suite is green locally on Ruby 4.0.9 in CI mode and against a database (except F-3). Adding `"4.0"` to the matrix is a small obvious-yes PR candidate.
- **F-11** needs Dewayne's decision. Major versions available for runtime dependencies locked well behind: ruby_llm 1.9.1 to 2.1.1, neighbor 0.6.0 to 1.2.0, rmagick 6.1.5 to 7.2.0, marcel 1.1.0 to 2.1.0 (transitive), json 2.18 to 3.1. The gemspec doesn't bound them, so users already resolve to the new majors; the lockfile, and so CI, still tests the old ones. Decide whether to move the lockfile to the current majors (testing what users actually get) or to add upper bounds.
- **F-12** open (environment, not repo). Ruby 4.1.0-preview1 with Bundler 2.7.2 crashes on this gemspec (`uninitialized constant Pathname::SEPARATOR_PAT`); work under Ruby 4.0.9. Revisit when Bundler ships a 4.1 fix.

### 2026-11-07: status check (no audit run; 5 verified open findings, last audit 1 day old)

Checked on develop 19bde2b: F-1, F-7, F-8, F-9, F-10 still present and open. F-3 is still the only failure in the full database suite (1294 runs). F-2 merged in #6. S-1 part 1 PR'd as #7; the remaining part needs a major bump, so it waits on F-11.

- **F-13** open (low). `Gemfile.lock` lists only precompiled nokogiri platforms; their gems cap Ruby below 4.1, and there is no `ruby` platform entry, so `bundle install` on 4.1 would re-resolve the lock. Moot until F-12 (Bundler on 4.1) is fixed. Raised by `/code-review` on #7 and kept out of it.
