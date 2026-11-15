# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-15 03:05._ madbot opened #81 (F-84), #82 (F-53, fixes #78) and #83 (F-79), and filed #84–#86; five bot PRs (#75, #76, #81–#83) now await Dewayne, none of them low risk.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo. **Dewayne opens his own PRs for bot issues (#47–#62, #72–#74); check open PRs from every author before building.**

- F-96: `HTM#restore` doesn't clear the query cache. [#75](https://github.com/MadBomber/htm/pull/75) **PR open**
- F-56: `Tag.with_prefix` / `ListTagsTool` don't escape LIKE wildcards. [#76](https://github.com/MadBomber/htm/pull/76) **PR open**
- F-53: DB passwords with URL-reserved characters are not encoded or decoded. Issue [#78](https://github.com/MadBomber/htm/issues/78). [#82](https://github.com/MadBomber/htm/pull/82) **PR open**
- F-131: documented embedding/tag/proposition timeouts never applied. Issue [#79](https://github.com/MadBomber/htm/issues/79).
- F-123: `htm:db:info` omits `node_relationships` (use `Database::TABLES`). Issue [#80](https://github.com/MadBomber/htm/issues/80). One line plus a test.
- F-37: move to minitest 6 (`~> 6.0` plus `minitest-mock`); verified green at b7eb5ef. Gemfile change; re-run at develop first.
- F-33: add `spec.metadata["documentation_uri"]` to `htm.gemspec` (site returns 200; one line).
- F-79: `rake examples:status` always says "Database connected: No". [#83](https://github.com/MadBomber/htm/pull/83) **PR open**
- F-84: `HTM_EXTRACT_PROPOSITIONS` in `examples/12_rails_app/README.md` and the root `.envrc`. [#81](https://github.com/MadBomber/htm/pull/81) **PR open**
- F-69: MCP tools use `instance_variable_get(:@long_term_memory)` instead of the reader (`mcp/tools.rb`; build after #76, same file).
- F-18: extract the duplicated working-memory eviction block in `lib/htm.rb` (after #75, same file).
- F-98: Railtie loads only 2 of 4 rake files; empty `htm.middleware` initializer. Needs railties in a scratch bundle for proof.
- F-20: `enqueue_tags_job` / `generate_tags_step` re-implement `Node#add_tags`.
- F-49: `htm:files:info` hand-rolls `FileSource.by_path`.
- F-66: session settings applied to one pooled connection only. Issue [#39](https://github.com/MadBomber/htm/issues/39). The `connect_sqls` half is small; the per-instance timeout needs a decision.
- F-78 (docs half): stale pre-renumbering example paths in `docs/` and `SETUP.md` (docs deploy; not low risk).
- F-86: unreachable `when DateTime` branches in `HTM::Timeframe` (plus the dead `DateTime` in `QueryCache#normalize_arg`). A removal.
- F-142: "Nodes with tags" counts tag links (`jobs.rake`, `mcp/tools.rb`). Issue [#84](https://github.com/MadBomber/htm/issues/84). The `tools.rb` half should wait for #76.
- F-143: `htm:jobs:process_tags` skips ancestor tags. Issue [#85](https://github.com/MadBomber/htm/issues/85). One line (`node.add_tags`) plus a task test.
- F-140: example DB scripts shell out to the libpq default server. Issue [#86](https://github.com/MadBomber/htm/issues/86). Examples-only.
- F-139 / F-137: docs list rake tasks that do not exist and call removed APIs (docs deploy; not low risk).
- F-144: `jobs.rake` helpers leak onto `Object`; `time_ago` has gaps between its ranges.

## Open bomberbot PRs

- [#83](https://github.com/MadBomber/htm/pull/83) fix(rake): examples:status connects through Sequel (F-79). **PR open**
- [#82](https://github.com/MadBomber/htm/pull/82) fix(config): percent-encode database user and password (F-53, fixes #78). **PR open**
- [#81](https://github.com/MadBomber/htm/pull/81) fix(examples): use HTM_PROPOSITION__ENABLED (F-84). **PR open**
- [#76](https://github.com/MadBomber/htm/pull/76) fix(tags): escape LIKE wildcards in tag prefix filters (F-56). **PR open**
- [#75](https://github.com/MadBomber/htm/pull/75) fix(cache): invalidate search cache in HTM#restore (F-96). **PR open**

## Merged bomberbot PRs

- [#77](https://github.com/MadBomber/htm/pull/77) fix(examples): drop unused amazing_print, fix stale paths (F-80, F-78 examples half). **Merged (low risk)** by madbot 2026-11-15, develop ad34812.
- [#68](https://github.com/MadBomber/htm/pull/68) fix(test): stop preloading test_helper in rake test (F-90). Squash-merged 2026-11-13.
- [#67](https://github.com/MadBomber/htm/pull/67) fix(propositions): read circuit breaker settings from config (F-99). Squash-merged 2026-11-13.
- [#66](https://github.com/MadBomber/htm/pull/66) fix(tags): anchor TagService::TAG_FORMAT to whole string (F-97). Squash-merged 2026-11-13.
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
