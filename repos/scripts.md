# scripts

Everyday scripts kept in sync across machines.

## Links

- GitHub: https://github.com/MadBomber/scripts
- RubyGems: https://rubygems.org/gems/scripts
- Issues: https://github.com/MadBomber/scripts/issues

## Status

_Updated 2026-11-07 (third run)._ #15 was merged; bomberbot opened [#16](https://github.com/MadBomber/scripts/pull/16) (**PR open**), which makes `new.rb` copy directory templates with `FileUtils.cp_r` (F-9). F-15 (`new Gemfile` never reaches the `.erb` fallback) is the next open item.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none yet)

## Open bomberbot PRs

- [#16](https://github.com/MadBomber/scripts/pull/16) fix(new): copy directory templates with FileUtils.cp_r (opened 2026-11-07), **PR open**

## Merged bomberbot PRs

- [#15](https://github.com/MadBomber/scripts/pull/15) fix(imbibler): run again on Ruby 3+ (merged 2026-11-07)
- [#14](https://github.com/MadBomber/scripts/pull/14) fix: replace Fixnum and File.exists? removed in Ruby 3.2 (merged 2026-11-07)
- [#13](https://github.com/MadBomber/scripts/pull/13) fix(templates): let sinatra_app resolve current gems (merged 2026-11-07)
