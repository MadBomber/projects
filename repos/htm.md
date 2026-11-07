# htm

Idle since May 2026, has open issues.

## Links

- GitHub: https://github.com/MadBomber/htm
- RubyGems: https://rubygems.org/gems/htm
- Issues: https://github.com/MadBomber/htm/issues

## Status

_Updated 2026-11-07._ The suite is green on Ruby 4.0.9 (633 runs); [#15](https://github.com/MadBomber/htm/pull/15) removes two dead `.bak` test files, and the first full dead-code audit found unreachable RRF helpers and a broken `ontology_structure` method, both waiting on Dewayne's decision.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-11: add a test for `LongTermMemory#topic_relationships` (no running test covers it). See [findings](../bomberbot/findings/htm.md).
- F-6: one autocorrectable rubocop offense in `hybrid_search.rb:324`.

## Open bomberbot PRs

- [#15](https://github.com/MadBomber/htm/pull/15) chore(test): remove stale .bak test files. **PR open**
- [#14](https://github.com/MadBomber/htm/pull/14) fix(test): suite passes with a custom HTM_SERVICE__NAME. Merged 2026-11-07.
