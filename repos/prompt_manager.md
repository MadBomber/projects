# prompt_manager

Prompt file management gem used by aia.

## Links

- GitHub: https://github.com/MadBomber/prompt_manager
- RubyGems: https://rubygems.org/gems/prompt_manager
- Issues: https://github.com/MadBomber/prompt_manager/issues

## Status

_Updated 2026-11-07 (second run)._ #20 merged; [#21](https://github.com/MadBomber/prompt_manager/pull/21) swaps the deprecated `SimpleCov.add_filter` for `skip` (CI green), **PR open**; the first adoption audit found README examples accurate and logged F-10 to F-15.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Replace the deprecated `SimpleCov.add_filter` with `SimpleCov.skip` in `test/test_helper.rb` (findings F-6). [#21](https://github.com/MadBomber/prompt_manager/pull/21) **PR open**.
- README: say that env var values are literal, like command output (findings F-11).
- Add an `llms.txt` (findings F-12).

## Open bomberbot PRs

- [#21](https://github.com/MadBomber/prompt_manager/pull/21) test: replace deprecated SimpleCov.add_filter with skip (**PR open**, targets develop; Test check green)

Merged: [#19](https://github.com/MadBomber/prompt_manager/pull/19), [#20](https://github.com/MadBomber/prompt_manager/pull/20) (both 2026-11-07, no comments).
