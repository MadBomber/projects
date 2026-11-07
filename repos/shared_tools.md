# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-07._ #5 (issue #3) merged; ComputerTool now loads the macos gem under aia, and its hold_key/scroll actions stop raising NameError; PR open.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Make `tool_classes_test.rb` locale-independent (findings F-2).
- ComputerTool: require `macos` on demand, the same fix as #5 (findings F-3). **PR open:** [#6](https://github.com/MadBomber/shared_tools/pull/6)

## Merged

- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (opened 2026-11-07)
