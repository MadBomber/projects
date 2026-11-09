# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-07 (third run)._ #7 merged; #8 pins the SimpleCov root and stops tracking the committed `test/coverage/` report; PR open.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Stop tracking the generated `test/coverage/` report (finding F-7). **PR open:** [#8](https://github.com/MadBomber/shared_tools/pull/8)

## Merged

- [#7](https://github.com/MadBomber/shared_tools/pull/7): fix(test): read lib files as UTF-8 in the DSL guard test (merged 2026-11-07, no comments)
- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (merged 2026-11-07, no comments)
- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#8](https://github.com/MadBomber/shared_tools/pull/8): fix(test): pin SimpleCov root and stop tracking test/coverage (opened 2026-11-07)
