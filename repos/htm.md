# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-10._ Queue was empty on arrival (Dewayne merged #20 and #22 into `develop`, now 27 ahead of `main`). This visit opened three PRs — [#25](https://github.com/MadBomber/htm/pull/25) (Fixes #21, rake unload false-success + the repo's first rake-task test), [#24](https://github.com/MadBomber/htm/pull/24) (Fixes #18, `unload_file` evicts chunks from working memory), [#23](https://github.com/MadBomber/htm/pull/23) (adoption: `docs/llms.txt`) — ran the first adoption audit (F-29–F-33), and filed issue #26 (database docs out of sync). Suite 646 green on Ruby 4.0.9 / PG18.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-6: one autocorrectable rubocop offense in `hybrid_search.rb:324`.
- F-18: extract the duplicated working-memory eviction block in `lib/htm.rb` (improver C2).
- F-19: `check_database_config!` defined twice (`mcp/cli.rb:140`, `mcp/server.rb:30`) — one shared method.
- F-20: `enqueue_tags_job` / `generate_tags_step` re-implement `Node#add_tags` (improver C1). Touches `lib/htm.rb` — stack on #24 or wait until it merges.
- F-33: add `spec.metadata["documentation_uri"]` to `htm.gemspec` (one line).
- F-21: `unload_file` drops chunks from working memory. Issue [#18](https://github.com/MadBomber/htm/issues/18). [#24](https://github.com/MadBomber/htm/pull/24) **PR open**
- F-25: `rake htm:files:unload` reports success for unknown files. Issue [#21](https://github.com/MadBomber/htm/issues/21). [#25](https://github.com/MadBomber/htm/pull/25) **PR open**
- F-29: `docs/llms.txt`. [#23](https://github.com/MadBomber/htm/pull/23) **PR open**

## Open bomberbot PRs

- [#25](https://github.com/MadBomber/htm/pull/25) fix(tasks): htm:files:unload reports not-found for unknown files (Fixes #21). **PR open**
- [#24](https://github.com/MadBomber/htm/pull/24) fix(files): unload_file drops chunks from working memory (Fixes #18). **PR open**
- [#23](https://github.com/MadBomber/htm/pull/23) docs: add llms.txt for AI discoverability (adoption; docs-deploy, not self-merge). **PR open**

## Merged bomberbot PRs

- [#22](https://github.com/MadBomber/htm/pull/22) docs(readme): drop stale version banner and fix Rails doc link. Merged 2026-11-09.
- [#20](https://github.com/MadBomber/htm/pull/20) fix(files): unload_file removes chunks and source together. Merged 2026-11-08.
- [#17](https://github.com/MadBomber/htm/pull/17) fix(files): unload_file cascades to node_tags and robot_nodes. Merged 2026-11-07.
- [#16](https://github.com/MadBomber/htm/pull/16) fix(tags): topic_relationships skips forgotten nodes. Merged 2026-11-07.
- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. Merged 2026-11-07.
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
