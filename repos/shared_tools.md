# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-12 07:30._ Second visit today skipped new PRs (queue full: #9, #13, #14 unreviewed, all MERGEABLE). Adoption audit ran (F-36..F-48); issues #15-#17 filed; #14's text reworded to neutral. `develop` is still 4 merged fixes ahead of v0.7.1 and issue #3 waits on a release.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Add test CI (F-6). **PR open:** [#9](https://github.com/MadBomber/shared_tools/pull/9)
- README examples and facts match the code (F-19 and improver items). **PR open:** [#13](https://github.com/MadBomber/shared_tools/pull/13)
- Normalize disk driver paths before the root check. **PR open:** [#14](https://github.com/MadBomber/shared_tools/pull/14)
- Remove broken leftover scripts at the root and in examples/ (F-26, F-27): [issue #11](https://github.com/MadBomber/shared_tools/issues/11)
- `Git::CheckoutTool` needs `--` after the ref (F-20): [issue #10](https://github.com/MadBomber/shared_tools/issues/10)
- Docs examples for EvalTool/ComputerTool raise ArgumentError (F-29): [issue #12](https://github.com/MadBomber/shared_tools/issues/12) (docs deploy from develop)
- `DataScienceKit` median and quartiles are wrong for even row counts (F-48): [issue #15](https://github.com/MadBomber/shared_tools/issues/15)
- Docs teach the ruby_llm 1.x `param` DSL and `Agent.new(tools:)` (F-36, F-37): [issue #16](https://github.com/MadBomber/shared_tools/issues/16) (docs deploy from develop)
- Clipboard tests overwrite the real clipboard and fail with no clipboard program (F-50, F-33): [issue #17](https://github.com/MadBomber/shared_tools/issues/17) (test-only)
- Raw NUL bytes make `process_start_tool.rb` and `search_codebase_tool.rb` binary to git and grep (F-25, widened)
- Dead Zeitwerk ignore lines and SimpleCov group (F-28): a three-line removal
- Doc links to renamed `*_example.rb` files, and a missing anchor (F-38, F-46)
- Open security items S-2, S-5, S-7, S-9 have small fixes (details held locally; neutral titles only)

## Merged bomberbot PRs

- [#8](https://github.com/MadBomber/shared_tools/pull/8): fix(test): pin SimpleCov root and stop tracking test/coverage (merged 2026-11-07, merge commit, no comments)
- [#7](https://github.com/MadBomber/shared_tools/pull/7): fix(test): read lib files as UTF-8 in the DSL guard test (merged 2026-11-07, no comments)
- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (merged 2026-11-07, no comments)
- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/shared_tools/pull/9): ci: run the Minitest suite on push and pull request (opened 2026-11-12; green on 3.3/3.4/4.0; review hit the cap)
- [#13](https://github.com/MadBomber/shared_tools/pull/13): docs(readme): match examples and facts to the code (opened 2026-11-12; 3 deferred review notes, so not self-merged)
- [#14](https://github.com/MadBomber/shared_tools/pull/14): fix(disk): normalize paths before the root check (opened 2026-11-12; lib change; review hit the cap)
