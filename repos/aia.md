# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-07 (third run)._ #158 and #159 merged; PR open so DelegateHandler and the Foreman tool require their own classes (F-10).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- `delegate_handler.rb` and `delegate_to_foreman_tool.rb` don't require the classes they use (findings F-10). [#160](https://github.com/MadBomber/aia/pull/160) (**PR open**)

## Open bomberbot PRs

- [#160](https://github.com/MadBomber/aia/pull/160) fix: DelegateHandler and Foreman tool require their classes (**PR open**)

## Merged bomberbot PRs

- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (merged 2026-11-07)
- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (merged 2026-11-07)
- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
