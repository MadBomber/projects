# scripts

Everyday scripts kept in sync across machines. Not a gem: its suite is whatever `test/` holds plus a clean load (`ruby -c`) of every script.

## Links

- GitHub: https://github.com/MadBomber/scripts
- RubyGems: https://rubygems.org/gems/scripts
- Issues: https://github.com/MadBomber/scripts/issues

## Status

_Updated 2026-11-10 (sixth run)._ Dewayne merged all five 2026-11-09 PRs (#17, #18, #20, #21, #22), but #22 merged into its stacked base `bomber/readme-fix-rows` after #18 had already merged, so its README fixes never reached develop; re-landed as [#25](https://github.com/MadBomber/scripts/pull/25) (Fixes #19). Also opened [#26](https://github.com/MadBomber/scripts/pull/26) (Fixes #23, F-14 copy step) and [#24](https://github.com/MadBomber/scripts/pull/24) (F-6). First full **security** audit: S-2..S-4 held locally. Filed [#27](https://github.com/MadBomber/scripts/issues/27) and [#28](https://github.com/MadBomber/scripts/issues/28).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- S-2: security finding, details held locally. Small fix ready to build with a neutral title; first item next visit. (added 2026-11-10)

## Open bomberbot PRs

- [#24](https://github.com/MadBomber/scripts/pull/24) fix(templates): raise MissingSystemEnvironmentVariable for APP_BIND (opened 2026-11-10, F-6), **PR open**
- [#25](https://github.com/MadBomber/scripts/pull/25) docs(readme): land script index fixes stranded by #22 (opened 2026-11-10, Fixes #19 + F-24), **PR open**
- [#26](https://github.com/MadBomber/scripts/pull/26) fix: copy with FileUtils so paths with spaces work (opened 2026-11-10, Fixes #23, F-14), **PR open**

## Merged bomberbot PRs

- [#22](https://github.com/MadBomber/scripts/pull/22) docs(readme): complete and correct script index (merged 2026-11-09 **into its stacked base `bomber/readme-fix-rows`, after #18 had already merged** — content never reached develop; re-landed 2026-11-10)
- [#20](https://github.com/MadBomber/scripts/pull/20) fix(prepend_to_file): report correct program name in usage (merged 2026-11-09)
- [#21](https://github.com/MadBomber/scripts/pull/21) fix: load gem spec YAML with unsafe_load for Psych 4+ (merged 2026-11-09)
- [#18](https://github.com/MadBomber/scripts/pull/18) docs(readme): fix wrong and dead script-index rows (merged 2026-11-09)
- [#17](https://github.com/MadBomber/scripts/pull/17) fix(new): resolve .erb template fallback before existence check (merged 2026-11-09)
- [#16](https://github.com/MadBomber/scripts/pull/16) fix(new): copy directory templates with FileUtils.cp_r (merged 2026-11-07)
- [#15](https://github.com/MadBomber/scripts/pull/15) fix(imbibler): run again on Ruby 3+ (merged 2026-11-07)
- [#14](https://github.com/MadBomber/scripts/pull/14) fix: replace Fixnum and File.exists? removed in Ruby 3.2 (merged 2026-11-07)
- [#13](https://github.com/MadBomber/scripts/pull/13) fix(templates): let sinatra_app resolve current gems (merged 2026-11-07)
