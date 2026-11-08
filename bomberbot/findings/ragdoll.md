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

Dead-code scan last covered commit: c4d2b4d (2026-11-08 improver scan of a412813..c4d2b4d, PR #9 test changes only; nothing removable. The improver also read the whole tree: examples/temp.rb is a removal candidate, F-23)

## Findings

### 2026-11-05: found while fixing the develop suite (no audit run)

- **F-1** open. `ImageDescriptionService` (`image_description_service.rb:148`) and `ImageToTextService` (`image_to_text_service.rb:128`) call `ragdoll_config.ruby_llm_config`, which `Ragdoll::Core::Config` no longer defines after the `anyway_config` migration. The NoMethodError is rescued and logged, so both silently fall back to the ENV Ollama endpoint. Needs the right config key for the Ollama endpoint; see the FIXME at line 147. **Correction 2026-11-08 (builder, #10):** the NoMethodError is caught by a method-level rescue in `configure_ruby_llm_globally`, so the whole `RubyLLM.configure` block is skipped; it does not fall back to the ENV endpoint. A naive fix would start overwriting the global RubyLLM keys and Ollama base from ENV on every image.
- **F-2** merged ([#6](https://github.com/MadBomber/ragdoll/pull/6), merged 2026-11-07). `Database.disconnect!` calls `ActiveRecord::Base.clear_all_connections!`, which ActiveRecord 8.1 removed (`connection_handler.clear_all_connections!` exists). Raises NoMethodError. Small obvious-yes PR candidate.
- **F-3** needs Dewayne's decision. `SearchEngineTest#test_search_similar_content_returns_empty_results_when_embedding_is_nil` (test line 92) expects `[]`; `search_similar_content` returns `{results: [], statistics: nil, execution_time_ms: …}`. Fails only with a database. Which return shape is the contract?
- **F-4** needs Dewayne's decision. `Database.connected?` (and `Client#healthy?`, `client.rb:515`) reports whether a connection is checked out, not whether the database is reachable, so it is false right after setup. Change the meaning or document it.
- **F-5** needs Dewayne's decision. `EmbeddingService#generate_fallback_embedding` seeds from the service's `object_id`, so every fallback vector from one service is identical and similarity among them is 1.0.
- **F-6** needs Dewayne's decision. With no client, the outer rescue in `generate_embeddings_batch` swallows real Ollama errors and returns fallback vectors with only a printed warning.
- **F-7** open. That warning (`embedding_service.rb:197`) is a `puts` in library code, writing to the host app's stdout.
- **F-8** merged ([#9](https://github.com/MadBomber/ragdoll/pull/9), merged 2026-11-07) for `client_test.rb` and `core_test.rb` ([#9](https://github.com/MadBomber/ragdoll/pull/9), 2026-11-07); the duplicate `ci_environment?` definitions are folded into F-16. Several test files (`test/core/client_test.rb`, `test/core/core_test.rb`) use `return if ci_environment?`, which counts as a pass in CI instead of a skip; `search_tracking_integration_test.rb:36` redefines `ci_environment?`.
- **F-9** issue [#13](https://github.com/MadBomber/ragdoll/issues/13). `ragdoll.gemspec` declares `activerecord` with no version; `ConnectionNotDefined` (used by the test hook) needs >= 7.1, currently guaranteed only through `neighbor`.

### 2026-11-06: dependencies audit (develop 66a9237)

Status check of earlier findings on 66a9237: F-1, F-7, F-8, F-9 still present and open. F-3 still the only failure in the full database suite (1294 runs). The develop suite merged via #4 and #5 is green in CI.

- **S-1** part 1 PR'd ([#7](https://github.com/MadBomber/ragdoll/pull/7), 2026-11-07); remainder needs Dewayne's decision. Security finding, details held locally. Most of it is an obvious-yes lockfile update within existing constraints; one part needs a major dependency bump and Dewayne's decision.
- **F-10** merged ([#8](https://github.com/MadBomber/ragdoll/pull/8), merged 2026-11-07). CI matrix (`.github/workflows/ruby.yml`) tests Ruby 3.2, 3.3, 3.4 only. The suite is green locally on Ruby 4.0.9 in CI mode and against a database (except F-3). Adding `"4.0"` to the matrix is a small obvious-yes PR candidate.
- **F-11** needs Dewayne's decision. Major versions available for runtime dependencies locked well behind: ruby_llm 1.9.1 to 2.1.1, neighbor 0.6.0 to 1.2.0, rmagick 6.1.5 to 7.2.0, marcel 1.1.0 to 2.1.0 (transitive), json 2.18 to 3.1. The gemspec doesn't bound them, so users already resolve to the new majors; the lockfile, and so CI, still tests the old ones. Decide whether to move the lockfile to the current majors (testing what users actually get) or to add upper bounds.
- **F-12** open (environment, not repo). Ruby 4.1.0-preview1 with Bundler 2.7.2 crashes on this gemspec (`uninitialized constant Pathname::SEPARATOR_PAT`); work under Ruby 4.0.9. Revisit when Bundler ships a 4.1 fix.

### 2026-11-07: status check (no audit run; 5 verified open findings, last audit 1 day old)

Checked on develop 19bde2b: F-1, F-7, F-8, F-9, F-10 still present and open. F-3 is still the only failure in the full database suite (1294 runs). F-2 merged in #6. S-1 part 1 PR'd as #7; the remaining part needs a major bump, so it waits on F-11.

- **F-13** open (low). `Gemfile.lock` lists only precompiled nokogiri platforms; their gems cap Ruby below 4.1, and there is no `ruby` platform entry, so `bundle install` on 4.1 would re-resolve the lock. Moot until F-12 (Bundler on 4.1) is fixed. Raised by `/code-review` on #7 and kept out of it.

### 2026-11-07, second run: status check (no audit run; 4 verified open findings, last audit 1 day old)

Checked on develop 8979006: F-1, F-7, F-8 and F-9 are still present and open. F-10 PR'd as #8, where the new Ruby 4.0 job passed (4.0.9 x86_64-linux, 1294 runs, 0 failures, 362 skips). Both new findings came up in `/code-review` on #8 and were kept out of it.

- **F-14** open. Every CI job sets `RAGDOLL_SKIP_DATABASE_TESTS: true` while starting a pgvector Postgres service, so CI never runs the database tests (362 skips) and the service container goes unused. The full database suite fails only on F-3 locally, so turning DB tests on in CI depends on F-3.
- **F-15** open (low). `Rakefile` line 4 sets `$VERBOSE = nil`, which hides every Ruby warning in `rake` runs, CI included, deprecation warnings for future Ruby versions among them.

### 2026-11-07, third run: status check (no audit run; 5 verified open findings, last audit 1 day old)

Checked on develop a412813: F-1, F-7, F-9, F-14 and F-15 are still present and open. F-8's 32 `return if ci_environment?` guards are PR'd as #9, which turns them into skips (CI-mode skips go from 362 to 394; the database suite is unchanged at 1294 runs with only the F-3 failure).

- **F-16** issue [#12](https://github.com/MadBomber/ragdoll/issues/12). Four test files hide all of their tests (43 in total) behind `private`: `test/integration/search_tracking_integration_test.rb` (6), `test/models/ragdoll/search_test.rb` (16), `test/models/ragdoll/search_result_test.rb` (15) and `test/models/ragdoll/search_cleanup_test.rb` (6). Each file puts `private` and its own copy of `ci_environment?` above its first test, and Minitest runs only public `test_*` methods, so each file reports 0 runs even with a database. When the tests are made public and run against a throwaway pgvector Postgres, 23 of the 43 fail or error, many because they call Rails-only `assert_not` / `assert_not_includes`. Fixing them is more than a one-line change: move `private` below the tests, drop the duplicate `ci_environment?`, and repair the 23 broken tests. It is probably a PR per file. RuboCop's `Minitest/NonPublicTestMethod` cop would catch this kind of mistake.

### 2026-11-08: status check plus improver read (no audit run; 5 verified open findings, last audit 2 days old)

Checked on develop c4d2b4d: F-1, F-7, F-9, F-14, F-15 and F-16 are still present. F-8 merged in #9. Triage made the 6 `search_cleanup_test.rb` tests public against a throwaway pgvector Postgres: they pass after dropping `private` and the duplicate `ci_environment?` and changing one `assert_not` to `refute` (1 error otherwise); the other three files have 6, 12 and 3 failures or errors. F-7 covers two `puts` sites (`embedding_service.rb:119` and `:197`), and app/ and lib/ hold about 95 non-comment `puts` in all (see F-26).

- **F-17** PR'd ([#10](https://github.com/MadBomber/ragdoll/pull/10), 2026-11-08). Image and audio files never reached their converters: `DocumentConverter` passed `@options` positionally to keyword-only `ImageToTextService.new` / `AudioToTextService.new` (ArgumentError), and `lib/ragdoll/core.rb` never required `audio_to_text_service` (NameError; `test_helper.rb` required it, hiding the gap). Both were rescued, so documents were stored with "Image file: x.png (N B)" placeholders.
- **F-18** issue [#11](https://github.com/MadBomber/ragdoll/issues/11). The README Quick Start configuration block uses six settings that no longer exist (`database_config=` and others) and raises NoMethodError on its first line; the link to `docs/docs/getting-started/configuration.md` is dead; line 353 shows `:ollama` where the value is `"ollama"`.
- **F-19** unverified. `ImageToTextService::DEFAULT_OPTIONS` includes `detail_level:`, which is passed to `RubyLLM.chat` and raises "unknown keyword: :detail_level", so the primary vision model is never built and only the text fallback runs. Noticed by the builder on #10.
- **F-20** unverified. `unified_document_management.rb:108` calls `@converter.convert_to_text(location, type, **options)`, but the instance method takes no keywords, so any non-empty options raise ArgumentError. Noticed by the builder on #10.
- **F-21** open. `test/test_helper.rb:196-201` requires `app/services` files directly, which hides missing requires in `lib/ragdoll/core.rb` (the cause of half of F-17). Fix: drop those lines, or add one fresh-process test that every `app/**/ragdoll/*.rb` file is loaded by `require "ragdoll"`.
- **F-22** open (low). The `test/core/test_image.png` fixture is corrupt (RMagick: `IDAT: incorrect data check`), and the suite leaves an untracked `test/tmp/` behind.
- **F-23** open. Every script in `examples/` sets configuration keys that the `anyway_config` migration removed (`use_unified_content=`, `embedding_model=`, `text_conversion=` and others), so none of them get past configuration. `examples/temp.rb` (97 lines, "Temporary test script for unified image processing") duplicates `examples/image_description.rb`, isn't referenced anywhere (README, CHANGELOG, Rakefile, gemspec, .github, other MadBomber repos) and isn't shipped in the gem: a removal candidate.
- **F-24** needs Dewayne's decision. Public methods that nothing references in app, lib, test, examples or README: `Document#embeddings_by_type`, `has_summary?`, `has_keywords?`, `add_keyword`, `remove_keyword`, `has_files?`, `total_file_size`, `primary_file_type`, `process_content!`; `Document.faceted_search`, `all_keywords`, `all_classifications`, `all_tags`, `keyword_frequencies`; `Search#ranked_results`; `DocumentProcessor.create_document_from_upload`; `TagService#add_tags_to_embedding`; `MetadataSchemas.required_fields_for`; `Config#active_xdg_config_file`, `refresh_ollama_models!`. `DocumentConverter::ConversionError` is defined but never raised. Keep as API, deprecate, or remove in a minor release?
- **F-25** needs Dewayne's decision. Loading the gem changes host-app globals: `lib/ragdoll.rb:3-5` and `lib/ragdoll/core.rb:4-6` `include DebugMe` at top level (mixing it into `Object`) and set `$DEBUG_ME = true`; `shrine_config.rb` defines top-level `FileUploader`, `ImageUploader`, `AudioUploader` and sets global `Shrine.storages` relative to the working directory. Moving them under `Ragdoll` is a compatibility change.
- **F-26** needs Dewayne's decision. Library logging convention: about 95 `puts` in app/ and lib/ (28 in `image_description_service.rb`, 16 in `migration_service.rb`) and no library logger. Options: a `Ragdoll.logger`, `warn` to stderr, or `debug_me`. Once chosen, each service is a small PR (F-7 included).
- **F-27** open (low). `AudioToTextService`'s OpenAI, Azure and Google transcription methods are placeholders that return fallback text.
- **S-2** open. Security finding, details held locally.
