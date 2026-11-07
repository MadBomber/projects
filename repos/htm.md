# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-07 (second run)._ [#16](https://github.com/MadBomber/htm/pull/16) adds the first running tests for `topic_relationships` and stops it counting soft-deleted data; the first security audit found nothing new beyond S-1.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-11: add a test for `LongTermMemory#topic_relationships` (no running test covers it). [#16](https://github.com/MadBomber/htm/pull/16) **PR open**
- F-14: make `FileSource#soft_delete_chunks!` (`htm.unload_file`) cascade to `node_tags` and `robot_nodes` like `Node#soft_delete!`. See [findings](../bomberbot/findings/htm.md).
- F-6: one autocorrectable rubocop offense in `hybrid_search.rb:324`.

## Open bomberbot PRs

- [#16](https://github.com/MadBomber/htm/pull/16) fix(tags): topic_relationships skips forgotten nodes. **PR open**
- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. Merged 2026-11-07.
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
