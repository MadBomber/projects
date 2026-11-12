# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-12 07:20._ Review queue still full (#30–#32 open, none approved), so no PRs were opened. Ran the second adoption audit (F-74–F-84), the improver added F-85–F-89, and filed #41 (timeframe phrases never cover the past), #42 (`forget`/`restore` string ids) and #43 (stale YARD reference).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-6 / F-61: rubocop not clean (one offense in `hybrid_search.rb:324` plus a `.rubocop.yml` warning). Issue [#37](https://github.com/MadBomber/htm/issues/37). Three-line PR.
- F-18: extract the duplicated working-memory eviction block in `lib/htm.rb` (improver C2).
- F-19 / F-50: `check_database_config!` defined twice and both ignore config files, so `htm_mcp --config` can't find the database. Issue [#33](https://github.com/MadBomber/htm/issues/33).
- F-20: `enqueue_tags_job` / `generate_tags_step` re-implement `Node#add_tags` (improver C1). Touches `lib/htm.rb` — stack on #24 or wait until it merges.
- F-33: add `spec.metadata["documentation_uri"]` to `htm.gemspec` (one line).
- F-15: re-adding a soft-deleted tag link. Issue [#19](https://github.com/MadBomber/htm/issues/19). [#32](https://github.com/MadBomber/htm/pull/32) **PR open**
- F-41: `HTM_PROPOSITION__ENABLED=false` doesn't disable extraction. [#31](https://github.com/MadBomber/htm/pull/31) **PR open**
- F-42: `htm_mcp help` env-var names and defaults are wrong. [#30](https://github.com/MadBomber/htm/pull/30) **PR open**. The docs half, plus wrong single-underscore names in four more pages, is issue [#34](https://github.com/MadBomber/htm/issues/34) (docs-deploy, so not self-merged).
- F-40: `Tag#restore!` and `RobotNode#restore!` raise; soft-deleted tag names can't be reused. Issue [#28](https://github.com/MadBomber/htm/issues/28).
- F-44/F-45: three private methods with no callers. Issue [#35](https://github.com/MadBomber/htm/issues/35). A removal; an easy next PR.
- F-49: `htm:files:info` hand-rolls `FileSource.by_path`.
- F-63: install guide's verification script calls removed `HTM` methods. Issue [#36](https://github.com/MadBomber/htm/issues/36) (docs-deploy, so not self-merged).
- F-53: DB passwords with URL-reserved characters aren't encoded/decoded. Small PR with unit tests, no DB needed.
- F-55 / F-56: `Node#nearest_neighbors(distance:)` ignored; `Tag.with_prefix` doesn't escape LIKE wildcards.
- F-67: `Database.drop` misses `node_relationships`, so `htm:db:reset` fails. Issue [#38](https://github.com/MadBomber/htm/issues/38). One line plus a test; best next PR.
- F-72: `HTM#recall` YARD gives the wrong default strategy. Issue [#40](https://github.com/MadBomber/htm/issues/40). Three comment lines.
- F-66: session settings applied to one pooled connection only. Issue [#39](https://github.com/MadBomber/htm/issues/39). The `connect_sqls` half is small; the per-instance timeout needs a decision.
- F-69: MCP tools use `instance_variable_get(:@long_term_memory)` instead of the reader (small swap).
- F-8: gemspec `required_ruby_version` floor is unreachable (plus the undeclared `logger`, F-36). Issue [#29](https://github.com/MadBomber/htm/issues/29).
- F-85: `TimeframeExtractor` builds future ranges for "this month/week/year" and "since X". Issue [#41](https://github.com/MadBomber/htm/issues/41). About 8 lib lines plus pure tests, no DB. Strong next PR.
- F-83: `HTM#forget`/`#restore` send non-integer ids to SQL. Issue [#42](https://github.com/MadBomber/htm/issues/42). Two lines plus tests; touches `lib/htm.rb` (overlaps #32 file).
- F-79: `rake examples:status` always says "Database connected: No" (calls removed `ActiveRecordConfig`). About six Rakefile lines.
- F-80: `examples/02_config_file_example/show_config.rb` requires `amazing_print`, which it never uses and the Gemfile lacks. One line; examples-only, so low risk.
- F-78 (examples half): example `# Usage:` headers, `examples_helper.rb`, `00_create_examples_db.rb` and `SETUP.md` still use pre-renumbering paths. Comment/output lines only; low risk. The `docs/` half is docs-deploy.
- F-86: unreachable `when DateTime` branches and private `normalize_datetime` in `HTM::Timeframe` (DateTime < Date). About 14 lines removed; a removal.
- F-77: generated YARD reference predates the Sequel migration. Issue [#43](https://github.com/MadBomber/htm/issues/43) (docs-deploy plus `mkdocs.yml`; not self-merged).

## Open bomberbot PRs

- [#32](https://github.com/MadBomber/htm/pull/32) fix(tags): re-link soft-deleted node_tags rows instead of inserting (Fixes #19). **PR open**
- [#31](https://github.com/MadBomber/htm/pull/31) fix(config): HTM_PROPOSITION__ENABLED=false now turns proposition extraction off. **PR open**
- [#30](https://github.com/MadBomber/htm/pull/30) fix(mcp): correct env var names and defaults in htm_mcp help. **PR open**

## Merged bomberbot PRs

- [#25](https://github.com/MadBomber/htm/pull/25) fix(tasks): htm:files:unload reports not-found for unknown files (Fixes #21). Squash-merged 2026-11-10.
- [#24](https://github.com/MadBomber/htm/pull/24) fix(files): unload_file drops chunks from working memory (Fixes #18). Squash-merged 2026-11-10.
- [#23](https://github.com/MadBomber/htm/pull/23) docs: add llms.txt. Merged 2026-11-10.
- [#22](https://github.com/MadBomber/htm/pull/22) docs(readme): drop stale version banner and fix Rails doc link. Merged 2026-11-09.
- [#20](https://github.com/MadBomber/htm/pull/20) fix(files): unload_file removes chunks and source together. Merged 2026-11-08.
- [#17](https://github.com/MadBomber/htm/pull/17) fix(files): unload_file cascades to node_tags and robot_nodes. Merged 2026-11-07.
- [#16](https://github.com/MadBomber/htm/pull/16) fix(tags): topic_relationships skips forgotten nodes. Merged 2026-11-07.
- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. Merged 2026-11-07.
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
