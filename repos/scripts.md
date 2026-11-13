# scripts

Everyday scripts kept in sync across machines. Not a gem: its suite is whatever `test/` holds plus a clean load (`ruby -c`) of every script.

## Links

- GitHub: https://github.com/MadBomber/scripts
- RubyGems: https://rubygems.org/gems/scripts
- Issues: https://github.com/MadBomber/scripts/issues

## Status

_Updated 2026-11-13 (thirteenth run)._ Dewayne merged #35/#36/#37 and fixed every BomberBot issue (#27-#49) himself in #50-#67; three new PRs are open (#69 S-9, #70 F-89, #71 F-81) and three issues were filed (#72-#74). main and develop have diverged both ways (main has `ruler` e746bef; develop has #51-#67).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- S-9: security finding, details held locally. [#69](https://github.com/MadBomber/scripts/pull/69) **PR open**. (added 2026-11-12)

## Open bomberbot PRs

- [#69](https://github.com/MadBomber/scripts/pull/69) chore: remove vendored CGI printenv that shadows /usr/bin/printenv (opened 2026-11-13, S-9 backlog), **PR open**
- [#70](https://github.com/MadBomber/scripts/pull/70) fix(resize_images): open images by their full path (opened 2026-11-13, F-89), **PR open**
- [#71](https://github.com/MadBomber/scripts/pull/71) fix(fix_perms): skip .git and restore modes from a subdirectory (opened 2026-11-13, F-81), **PR open**

## Merged bomberbot PRs

- [#37](https://github.com/MadBomber/scripts/pull/37) fix(backup_envrc): copy with FileUtils, keep backups private (merged 2026-11-12, S-2)
- [#35](https://github.com/MadBomber/scripts/pull/35) fix(shc): read the filename argument instead of stdin (merged 2026-11-12, Fixes #32)
- [#36](https://github.com/MadBomber/scripts/pull/36) fix(yaml2json): allow YAML aliases and merge keys (merged 2026-11-12, Fixes #30)
- [#25](https://github.com/MadBomber/scripts/pull/25) docs(readme): land script index fixes stranded by #22 (merged 2026-11-10; Fixes #19, closed 2026-11-13)
- [#26](https://github.com/MadBomber/scripts/pull/26) fix: copy with FileUtils so paths with spaces work (merged 2026-11-10; Fixes #23, closed 2026-11-13)
- [#24](https://github.com/MadBomber/scripts/pull/24) fix(templates): raise MissingSystemEnvironmentVariable for APP_BIND (merged 2026-11-10)
- [#22](https://github.com/MadBomber/scripts/pull/22) docs(readme): complete and correct script index (merged 2026-11-09 **into its stacked base `bomber/readme-fix-rows`, after #18 had already merged** — content never reached develop; re-landed 2026-11-10)
- [#20](https://github.com/MadBomber/scripts/pull/20) fix(prepend_to_file): report correct program name in usage (merged 2026-11-09)
- [#21](https://github.com/MadBomber/scripts/pull/21) fix: load gem spec YAML with unsafe_load for Psych 4+ (merged 2026-11-09)
- [#18](https://github.com/MadBomber/scripts/pull/18) docs(readme): fix wrong and dead script-index rows (merged 2026-11-09)
- [#17](https://github.com/MadBomber/scripts/pull/17) fix(new): resolve .erb template fallback before existence check (merged 2026-11-09)
- [#16](https://github.com/MadBomber/scripts/pull/16) fix(new): copy directory templates with FileUtils.cp_r (merged 2026-11-07)
- [#15](https://github.com/MadBomber/scripts/pull/15) fix(imbibler): run again on Ruby 3+ (merged 2026-11-07)
- [#14](https://github.com/MadBomber/scripts/pull/14) fix: replace Fixnum and File.exists? removed in Ruby 3.2 (merged 2026-11-07)
- [#13](https://github.com/MadBomber/scripts/pull/13) fix(templates): let sinatra_app resolve current gems (merged 2026-11-07)
