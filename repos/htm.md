# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-13 06:20._ Dewayne merged #30–#32 and opened his own PRs #47–#62 for every open bomberbot issue, so madbot closed its duplicates (#63–#65) and opened #66–#68 on findings nobody else is working on. Third dependencies audit: F-90–F-93.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo. **Dewayne has his own PRs open (#47–#62) for most issue-backed items; check open PRs from every author before building.**

- Covered by Dewayne's open PRs (don't build): F-6/F-61 [#54](https://github.com/MadBomber/htm/pull/54) (#37); F-67 [#53](https://github.com/MadBomber/htm/pull/53) (#38); F-85 [#51](https://github.com/MadBomber/htm/pull/51) (#41); F-72 [#52](https://github.com/MadBomber/htm/pull/52) (#40); F-83 [#49](https://github.com/MadBomber/htm/pull/49) (#42); F-40 [#58](https://github.com/MadBomber/htm/pull/58) (#28); F-8/F-36 [#57](https://github.com/MadBomber/htm/pull/57) (#29); F-19/F-50 [#56](https://github.com/MadBomber/htm/pull/56) (#33); F-44/F-45 [#55](https://github.com/MadBomber/htm/pull/55) (#35); F-63 [#60](https://github.com/MadBomber/htm/pull/60) (#36); F-42 docs [#61](https://github.com/MadBomber/htm/pull/61) (#34); F-77 [#62](https://github.com/MadBomber/htm/pull/62) (#43); F-30 [#59](https://github.com/MadBomber/htm/pull/59) (#26); F-62 [#48](https://github.com/MadBomber/htm/pull/48) (#45); F-94 [#50](https://github.com/MadBomber/htm/pull/50) (#44); F-95 [#47](https://github.com/MadBomber/htm/pull/47) (#46).
- F-90: `rake test` runs 0 tests on Ruby 4.1 (Rakefile preloads test_helper). [#68](https://github.com/MadBomber/htm/pull/68) **PR open**
- F-97: `TagService::TAG_FORMAT` accepts multi-line tags. [#66](https://github.com/MadBomber/htm/pull/66) **PR open**
- F-99: `PropositionService` ignores circuit-breaker config. [#67](https://github.com/MadBomber/htm/pull/67) **PR open**
- F-37: move to minitest 6 (`~> 6.0` plus `minitest-mock`); verified green at b7eb5ef. Gemfile change; re-run at develop first.
- F-96: `HTM#restore` doesn't clear the query cache. One line plus a DB test; wait for Dewayne's #49 (same method).
- F-98: Railtie loads only 2 of 4 rake files; empty `htm.middleware` initializer. Needs railties in a scratch bundle for proof.
- F-33: add `spec.metadata["documentation_uri"]` to `htm.gemspec` (one line; check #57 first, it edits the gemspec).
- F-18: extract the duplicated working-memory eviction block in `lib/htm.rb`.
- F-20: `enqueue_tags_job` / `generate_tags_step` re-implement `Node#add_tags`.
- F-49: `htm:files:info` hand-rolls `FileSource.by_path`.
- F-53: DB passwords with URL-reserved characters aren't encoded/decoded.
- F-55 / F-56: `Node#nearest_neighbors(distance:)` ignored; `Tag.with_prefix` doesn't escape LIKE wildcards.
- F-66: session settings applied to one pooled connection only. Issue [#39](https://github.com/MadBomber/htm/issues/39). The `connect_sqls` half is small; the per-instance timeout needs a decision.
- F-69: MCP tools use `instance_variable_get(:@long_term_memory)` instead of the reader.
- F-79: `rake examples:status` always says "Database connected: No".
- F-80: `examples/02_config_file_example/show_config.rb` requires unused `amazing_print`. Low risk.
- F-78 (examples half): stale pre-renumbering example paths. Low risk.
- F-86: unreachable `when DateTime` branches in `HTM::Timeframe` (plus the dead `DateTime` in `QueryCache#normalize_arg`). A removal.

## Open bomberbot PRs

- [#68](https://github.com/MadBomber/htm/pull/68) fix(test): stop preloading test_helper in rake test (F-90). **PR open**
- [#67](https://github.com/MadBomber/htm/pull/67) fix(propositions): read circuit breaker settings from config (F-99). **PR open**
- [#66](https://github.com/MadBomber/htm/pull/66) fix(tags): anchor TagService::TAG_FORMAT to whole string (F-97). **PR open**

## Merged bomberbot PRs

- [#32](https://github.com/MadBomber/htm/pull/32) fix(tags): re-link soft-deleted node_tags rows instead of inserting (Fixes #19). Squash-merged 2026-11-12.
- [#31](https://github.com/MadBomber/htm/pull/31) fix(config): HTM_PROPOSITION__ENABLED=false now turns proposition extraction off. Squash-merged 2026-11-12.
- [#30](https://github.com/MadBomber/htm/pull/30) fix(mcp): correct env var names and defaults in htm_mcp help. Squash-merged 2026-11-12.
- [#25](https://github.com/MadBomber/htm/pull/25) fix(tasks): htm:files:unload reports not-found for unknown files (Fixes #21). Squash-merged 2026-11-10.
- [#24](https://github.com/MadBomber/htm/pull/24) fix(files): unload_file drops chunks from working memory (Fixes #18). Squash-merged 2026-11-10.
- [#23](https://github.com/MadBomber/htm/pull/23) docs: add llms.txt. Merged 2026-11-10.
- [#22](https://github.com/MadBomber/htm/pull/22) docs(readme): drop stale version banner and fix Rails doc link. Merged 2026-11-09.
- [#20](https://github.com/MadBomber/htm/pull/20) fix(files): unload_file removes chunks and source together. Merged 2026-11-08.
- [#17](https://github.com/MadBomber/htm/pull/17) fix(files): unload_file cascades to node_tags and robot_nodes. Merged 2026-11-07.
- [#16](https://github.com/MadBomber/htm/pull/16) fix(tags): topic_relationships skips forgotten nodes. Merged 2026-11-07.
- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. Merged 2026-11-07.
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
