# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-07 (second run)._ #158 still open, current with develop; PR open so failing test runs exit non-zero (F-11); F-10 is next.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Test runs exit 0 even when tests fail: `test/test_helper.rb`'s `Kernel#exit` override swallows Minitest's `exit(false)` (findings F-11). [#159](https://github.com/MadBomber/aia/pull/159) (**PR open**)
- `delegate_handler.rb` and `delegate_to_foreman_tool.rb` don't require the classes they use (findings F-10).

## Open bomberbot PRs

- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (**PR open**)
- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (**PR open**)

## Merged bomberbot PRs

- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
