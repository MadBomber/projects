# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-07 (second run)._ #6 merged; #7 makes the DSL guard test pass with no locale set and on Ruby 3.3; PR open.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make `tool_classes_test.rb` locale-independent (findings F-2, with F-9). **PR open:** [#7](https://github.com/MadBomber/shared_tools/pull/7)

## Merged

- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (merged 2026-11-07, no comments)
- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#7](https://github.com/MadBomber/shared_tools/pull/7): fix(test): read lib files as UTF-8 in the DSL guard test (opened 2026-11-07)
