# scripts

Everyday scripts kept in sync across machines. Not a gem: its suite is whatever `test/` holds plus a clean load (`ruby -c`) of every script.

## Links

- GitHub: https://github.com/MadBomber/scripts
- RubyGems: https://rubygems.org/gems/scripts
- Issues: https://github.com/MadBomber/scripts/issues

## Status

_Updated 2026-11-15 (seventeenth run)._ Review queue full (#95-#97 unreviewed), so no new PRs; dependencies audit (F-153..F-158) and an improver pass (F-175..F-182) ran, and #98 (slugify overwrites files), #99 (lflf2lf, F-150) and #100 (remadd -a, F-151) were filed. Next PR candidates once the queue clears: F-154 (`templates/cli.py` python3 shebang), F-176 (`breakout_git_revisions` via git archive), F-134/F-136 (pure deletions). main and develop still diverged both ways (`ruler` on main).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-150: `lflf2lf` has the same unguarded sed/mv as the old `cr2lf`, and never collapses blank lines; same fix shape as #96. Now issue [#99](https://github.com/MadBomber/scripts/issues/99). (added 2026-11-15)
- F-154: `templates/cli.py` uses `#!/usr/bin/env python`, missing on macOS; change to `python3`. (added 2026-11-15)

## Open bomberbot PRs

- [#95](https://github.com/MadBomber/scripts/pull/95) fix(sync_repo): stop before pushing when checkout or merge fails (opened 2026-11-15, F-145), **PR open**
- [#96](https://github.com/MadBomber/scripts/pull/96) fix(cr2lf): never empty a file it cannot read (opened 2026-11-15, F-130), **PR open**
- [#97](https://github.com/MadBomber/scripts/pull/97) fix(remadd): schedule "last <weekday>" in the right month (opened 2026-11-15, F-128), **PR open**

## Merged bomberbot PRs

- [#71](https://github.com/MadBomber/scripts/pull/71) fix(fix_perms): skip .git and restore modes from a subdirectory (merged 2026-11-13, F-81)
- [#70](https://github.com/MadBomber/scripts/pull/70) fix(resize_images): open images by their full path (merged 2026-11-13, F-89)
- [#69](https://github.com/MadBomber/scripts/pull/69) chore: remove vendored CGI printenv that shadows /usr/bin/printenv (merged 2026-11-13, S-9)
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
