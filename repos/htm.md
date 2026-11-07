# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-07 (third run)._ [#17](https://github.com/MadBomber/htm/pull/17) makes `unload_file` soft-delete its chunks' tag and robot links like `forget` does; the first quality audit found three small duplication items (F-18 to F-20).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-14: make `FileSource#soft_delete_chunks!` (`htm.unload_file`) cascade to `node_tags` and `robot_nodes` like `Node#soft_delete!`. [#17](https://github.com/MadBomber/htm/pull/17) **PR open**
- F-6: one autocorrectable rubocop offense in `hybrid_search.rb:324`.
- F-23: run `unload_file`'s cascade and `source.delete` in one transaction.
- F-18: extract the duplicated working-memory eviction block in `lib/htm.rb`.

## Open bomberbot PRs

- [#17](https://github.com/MadBomber/htm/pull/17) fix(files): unload_file cascades to node_tags and robot_nodes. **PR open**
- [#16](https://github.com/MadBomber/htm/pull/16) fix(tags): topic_relationships skips forgotten nodes. Merged 2026-11-07.
- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. Merged 2026-11-07.
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
