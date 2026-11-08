# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-08._ [#20](https://github.com/MadBomber/htm/pull/20) makes `unload_file` remove a file's chunks and its source in one transaction (new `FileSource#unload!`); #17 merged, and issues #18, #19 and #21 record verified bugs (F-21, F-15, F-25).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-6: one autocorrectable rubocop offense in `hybrid_search.rb:324`.
- F-23: run `unload_file`'s cascade and `source.delete` in one transaction. [#20](https://github.com/MadBomber/htm/pull/20) **PR open**
- F-18: extract the duplicated working-memory eviction block in `lib/htm.rb`.
- F-21: `unload_file` should drop the unloaded chunks from working memory, like `forget`. Issue [#18](https://github.com/MadBomber/htm/issues/18); do it after #20 merges (same lines).
- F-25: `rake htm:files:unload` prints "Unloaded:" for unknown files. Issue [#21](https://github.com/MadBomber/htm/issues/21).

## Open bomberbot PRs

- [#20](https://github.com/MadBomber/htm/pull/20) fix(files): unload_file removes chunks and source together. **PR open**

## Merged bomberbot PRs

- [#17](https://github.com/MadBomber/htm/pull/17) fix(files): unload_file cascades to node_tags and robot_nodes. Merged 2026-11-07.
- [#16](https://github.com/MadBomber/htm/pull/16) fix(tags): topic_relationships skips forgotten nodes. Merged 2026-11-07.
- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. Merged 2026-11-07.
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
