# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-06._ First bomberbot pass: fixed browser driver loading under aia (issue #3); PR open.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Fix issue #3, BrowserTool LoadError under aia. **PR open:** [#5](https://github.com/MadBomber/shared_tools/pull/5)
- Make `tool_classes_test.rb` locale-independent (findings F-2).
- ComputerTool: require `macos` on demand, the same fix as #5 (findings F-3).

## Open bomberbot PRs

- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (opened 2026-11-06)
