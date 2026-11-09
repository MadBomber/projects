# scripts

Everyday scripts kept in sync across machines. Not a gem: its suite is whatever `test/` holds plus a clean load (`ruby -c`) of every script.

## Links

- GitHub: https://github.com/MadBomber/scripts
- RubyGems: https://rubygems.org/gems/scripts
- Issues: https://github.com/MadBomber/scripts/issues

## Status

_Updated 2026-11-09 (fifth run)._ Nothing to merge/tend ([#17](https://github.com/MadBomber/scripts/pull/17), [#18](https://github.com/MadBomber/scripts/pull/18) both still unreviewed; no CI). Three new PRs: [#21](https://github.com/MadBomber/scripts/pull/21) fixes a Psych-4 `YAML.load` crash in three gem-spec scripts (F-5), [#20](https://github.com/MadBomber/scripts/pull/20) makes `prepend_to_file.rb` print its real program name (F-25), and [#22](https://github.com/MadBomber/scripts/pull/22) completes the README script index (Fixes [#19](https://github.com/MadBomber/scripts/issues/19)) and corrects 3 more wrong rows (F-24), stacked on #18. First full **dead-code** audit ran (whole tree at 543c66a): re-confirmed F-10/F-17/F-18 removable, new F-23 (`estimate_tokens.rb` inert) — all fix-vs-retire in the inbox. Filed [#23](https://github.com/MadBomber/scripts/issues/23) for F-14 (unquoted `cp -R` paths break on spaces).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#17](https://github.com/MadBomber/scripts/pull/17) fix(new): resolve .erb template fallback before existence check (opened 2026-11-09, F-15), **PR open**
- [#18](https://github.com/MadBomber/scripts/pull/18) docs(readme): fix wrong and dead script-index rows (opened 2026-11-09), **PR open**
- [#20](https://github.com/MadBomber/scripts/pull/20) fix(prepend_to_file): report correct program name in usage (opened 2026-11-09, F-25), **PR open**
- [#21](https://github.com/MadBomber/scripts/pull/21) fix: load gem spec YAML with unsafe_load for Psych 4+ (opened 2026-11-09, F-5), **PR open**
- [#22](https://github.com/MadBomber/scripts/pull/22) docs(readme): complete and correct script index (opened 2026-11-09, Fixes #19 + F-24; stacks on #18), **PR open**

## Merged bomberbot PRs

- [#16](https://github.com/MadBomber/scripts/pull/16) fix(new): copy directory templates with FileUtils.cp_r (merged 2026-11-07)
- [#15](https://github.com/MadBomber/scripts/pull/15) fix(imbibler): run again on Ruby 3+ (merged 2026-11-07)
- [#14](https://github.com/MadBomber/scripts/pull/14) fix: replace Fixnum and File.exists? removed in Ruby 3.2 (merged 2026-11-07)
- [#13](https://github.com/MadBomber/scripts/pull/13) fix(templates): let sinatra_app resolve current gems (merged 2026-11-07)
