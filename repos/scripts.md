# scripts

Everyday scripts kept in sync across machines. Not a gem: its suite is whatever `test/` holds plus a clean load (`ruby -c`) of every script.

## Links

- GitHub: https://github.com/MadBomber/scripts
- RubyGems: https://rubygems.org/gems/scripts
- Issues: https://github.com/MadBomber/scripts/issues

## Status

_Updated 2026-11-09 (fourth run)._ #16 merged (Dewayne). Two new PRs open: [#17](https://github.com/MadBomber/scripts/pull/17) fixes the `new.rb` `.erb`-fallback bug (F-15, the standing backlog item), and [#18](https://github.com/MadBomber/scripts/pull/18) corrects 6 wrong and removes 4 dead rows in the README script index. Filed [#19](https://github.com/MadBomber/scripts/issues/19) for the 22 tracked scripts still missing from that index. First-ever **quality** audit ran (reek over all top-level scripts): F-17/F-18 (two scripts that cannot run) and F-19 (79 files with 3+ consecutive `puts`), all fix-vs-retire/style decisions in the inbox.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#17](https://github.com/MadBomber/scripts/pull/17) fix(new): resolve .erb template fallback before existence check (opened 2026-11-09, F-15), **PR open**
- [#18](https://github.com/MadBomber/scripts/pull/18) docs(readme): fix wrong and dead script-index rows (opened 2026-11-09), **PR open**

## Merged bomberbot PRs

- [#16](https://github.com/MadBomber/scripts/pull/16) fix(new): copy directory templates with FileUtils.cp_r (merged 2026-11-07)
- [#15](https://github.com/MadBomber/scripts/pull/15) fix(imbibler): run again on Ruby 3+ (merged 2026-11-07)
- [#14](https://github.com/MadBomber/scripts/pull/14) fix: replace Fixnum and File.exists? removed in Ruby 3.2 (merged 2026-11-07)
- [#13](https://github.com/MadBomber/scripts/pull/13) fix(templates): let sinatra_app resolve current gems (merged 2026-11-07)
