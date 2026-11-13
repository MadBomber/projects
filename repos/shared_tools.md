# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-13 07:30._ Review queue full (#24, #28, #29 open, green, unreviewed), so no new PRs; a dead-code audit and the improver yielded F-68..F-86 and issues #30-#32. Dewayne's #18-#23 still open.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Remove broken leftover scripts at the root and in examples/ (F-26, F-27): [issue #11](https://github.com/MadBomber/shared_tools/issues/11); Dewayne's [#19](https://github.com/MadBomber/shared_tools/pull/19) open
- `Git::CheckoutTool` needs `--` after the ref (F-20): [issue #10](https://github.com/MadBomber/shared_tools/issues/10); Dewayne's [#18](https://github.com/MadBomber/shared_tools/pull/18) open
- Docs examples for EvalTool/ComputerTool raise ArgumentError (F-29): [issue #12](https://github.com/MadBomber/shared_tools/issues/12) (docs deploy from develop); Dewayne's [#22](https://github.com/MadBomber/shared_tools/pull/22) open
- `DataScienceKit` median and quartiles are wrong for even row counts (F-48): [issue #15](https://github.com/MadBomber/shared_tools/issues/15); Dewayne's [#20](https://github.com/MadBomber/shared_tools/pull/20) open
- Docs teach the ruby_llm 1.x `param` DSL and `Agent.new(tools:)` (F-36, F-37): [issue #16](https://github.com/MadBomber/shared_tools/issues/16) (docs deploy from develop); Dewayne's [#23](https://github.com/MadBomber/shared_tools/pull/23) open
- Clipboard tests overwrite the real clipboard and fail with no clipboard program (F-50, F-33): [issue #17](https://github.com/MadBomber/shared_tools/issues/17) (test-only); Dewayne's [#21](https://github.com/MadBomber/shared_tools/pull/21) open
- Raw NUL bytes make `process_start_tool.rb` and `search_codebase_tool.rb` binary to git and grep (F-25, widened). **PR open:** [#28](https://github.com/MadBomber/shared_tools/pull/28)
- Dead Zeitwerk ignore lines and SimpleCov group (F-28). **PR open:** [#24](https://github.com/MadBomber/shared_tools/pull/24)
- Doc links to renamed `*_example.rb` files, and a missing anchor (F-38, F-46)
- `notification_tool` and `search_codebase_tool` have no require shim (F-55). **PR open:** [#29](https://github.com/MadBomber/shared_tools/pull/29)
- `CronTool#next_times` fails during minute :59 (F-59): [issue #25](https://github.com/MadBomber/shared_tools/issues/25)
- `CronTool` field parsing and day matching (F-60..F-62): [issue #26](https://github.com/MadBomber/shared_tools/issues/26)
- Tool files raise NameError when required directly (F-65): [issue #27](https://github.com/MadBomber/shared_tools/issues/27) (after #20 merges, for `data_science_kit.rb`)
- `CompositeAnalysisTool` returns simulated data as if real (F-78): [issue #30](https://github.com/MadBomber/shared_tools/issues/30) (two PRs: files, then URLs)
- `CompositeAnalysisTool`/`DataScienceKit` ignore their nested `options`/`parameters` object (F-79): [issue #31](https://github.com/MadBomber/shared_tools/issues/31) (DataScienceKit half after #20)
- `WorkflowManagerTool` creates `.workflows/` on `new` (F-80): [issue #32](https://github.com/MadBomber/shared_tools/issues/32)
- `DiskTool`/`ComputerTool` return nil for unknown actions (F-68); macOS notification text with `#{` fails (F-77); WHOIS read has no timeout (F-82); dead `storage_dir` reader (F-70); committed `examples/.workflows/*.json` (F-73)
- Open security items S-2, S-5, S-7, S-9, S-12, S-13 have small fixes (details held locally; neutral titles only)

## Merged bomberbot PRs

- [#14](https://github.com/MadBomber/shared_tools/pull/14): fix(disk): normalize paths before the root check (merged 2026-11-12, merge commit, no comments)
- [#13](https://github.com/MadBomber/shared_tools/pull/13): docs(readme): match examples and facts to the code (merged 2026-11-12, merge commit, no comments)
- [#9](https://github.com/MadBomber/shared_tools/pull/9): ci: run the Minitest suite on push and pull request (merged 2026-11-12, merge commit, no comments)
- [#8](https://github.com/MadBomber/shared_tools/pull/8): fix(test): pin SimpleCov root and stop tracking test/coverage (merged 2026-11-07, merge commit, no comments)
- [#7](https://github.com/MadBomber/shared_tools/pull/7): fix(test): read lib files as UTF-8 in the DSL guard test (merged 2026-11-07, no comments)
- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (merged 2026-11-07, no comments)
- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#24](https://github.com/MadBomber/shared_tools/pull/24): chore: drop loader ignores for paths that no longer exist (opened 2026-11-13; F-28; lib removal, green, review clean)
- [#28](https://github.com/MadBomber/shared_tools/pull/28): chore: escape NUL bytes in string literals (opened 2026-11-13; F-25; lib, green, review clean; guard test in its own file to avoid conflicting with #23)
- [#29](https://github.com/MadBomber/shared_tools/pull/29): fix: add require shims for notification and search_codebase tools (opened 2026-11-13; F-55; lib, green, review clean)
