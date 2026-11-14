# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-14 12:50._ Dewayne merged #18-#24, #28, #29, #33, #36-#38; mini_madbot opened #42 (docs links), #43 (unknown actions), #44 (macOS notification quoting), all green and awaiting review; dependencies audit filed #39-#41.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Doc links to renamed `*_example.rb` files, and a missing anchor (F-38, F-46). **PR open:** [#42](https://github.com/MadBomber/shared_tools/pull/42)
- `DiskTool`/`ComputerTool` return nil for unknown actions (F-68). **PR open:** [#43](https://github.com/MadBomber/shared_tools/pull/43)
- macOS notification text with `#{` fails (F-77). **PR open:** [#44](https://github.com/MadBomber/shared_tools/pull/44)
- `CronTool` field parsing and day matching (F-60..F-62): [issue #26](https://github.com/MadBomber/shared_tools/issues/26); Dewayne's [#34](https://github.com/MadBomber/shared_tools/pull/34) open
- Tool files raise NameError when required directly (F-65): [issue #27](https://github.com/MadBomber/shared_tools/issues/27); Dewayne's [#35](https://github.com/MadBomber/shared_tools/pull/35) open (touches first lines of 11 tool files and `require_shims_test.rb`; wait for it before load/shim work)
- WHOIS read has no timeout (F-82): [issue #39](https://github.com/MadBomber/shared_tools/issues/39)
- Gemspec allows zeitwerk < 2.6 (F-87): [issue #40](https://github.com/MadBomber/shared_tools/issues/40) (gemspec, never low risk)
- HTTP tools fail when the first resolved address is unreachable IPv6 (F-91): [issue #41](https://github.com/MadBomber/shared_tools/issues/41) (security-adjacent code: neutral wording)
- CHANGELOG `## Unreleased` has no line for #29 (require shims); add at the end of `#### Fixes` once #34/#35 merge
- Confirmation prompt prints `(y/N` (F-51); README:87/675 show prompts the code never prints (fold with F-51; see S-14 locally)
- Test-only: retry tests sleep for real (F-52); RubyLLM INFO logs flood test output (F-53); SimpleCov deprecations (F-89, unverified)
- Examples-only: `examples/common.rb`/README describe the wrong default provider and `OLLAMA_HOST` (F-45); committed `examples/.workflows/*.json` (F-73)
- Dead `storage_dir` reader (F-70); unused `n_features` local in `data_science_kit.rb:661` (after #34/#35 merge)
- `CompositeAnalysisTool` median picks the upper middle (F-83; after #34/#35 merge)
- `docs/llms.txt` (F-43; deploys from develop)
- Open security items S-2, S-5, S-7, S-9, S-12, S-13, S-14 have small fixes (details held locally; neutral titles only)

## Merged bomberbot PRs

- [#29](https://github.com/MadBomber/shared_tools/pull/29): fix: add require shims for notification and search_codebase tools (merged 2026-11-13 by Dewayne, squash 6afcd50, no comments)
- [#28](https://github.com/MadBomber/shared_tools/pull/28): chore: escape NUL bytes in string literals (merged 2026-11-13, squash 7108591, no comments)
- [#24](https://github.com/MadBomber/shared_tools/pull/24): chore: drop loader ignores for paths that no longer exist (merged 2026-11-13, squash 52aaee3, no comments)
- [#14](https://github.com/MadBomber/shared_tools/pull/14): fix(disk): normalize paths before the root check (merged 2026-11-12, merge commit, no comments)
- [#13](https://github.com/MadBomber/shared_tools/pull/13): docs(readme): match examples and facts to the code (merged 2026-11-12, merge commit, no comments)
- [#9](https://github.com/MadBomber/shared_tools/pull/9): ci: run the Minitest suite on push and pull request (merged 2026-11-12, merge commit, no comments)
- [#8](https://github.com/MadBomber/shared_tools/pull/8): fix(test): pin SimpleCov root and stop tracking test/coverage (merged 2026-11-07, merge commit, no comments)
- [#7](https://github.com/MadBomber/shared_tools/pull/7): fix(test): read lib files as UTF-8 in the DSL guard test (merged 2026-11-07, no comments)
- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (merged 2026-11-07, no comments)
- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#42](https://github.com/MadBomber/shared_tools/pull/42): docs(examples): point links at renamed *_demo.rb files (opened 2026-11-14; F-38, F-46; docs-only, deploys from develop, needs-dewayne; green, review clean)
- [#43](https://github.com/MadBomber/shared_tools/pull/43): fix(tools): error on unknown disk/computer action (opened 2026-11-14; F-68; lib+test+CHANGELOG, green, review clean)
- [#44](https://github.com/MadBomber/shared_tools/pull/44): fix(notification): quote macOS text as AppleScript strings (opened 2026-11-14; F-77; lib+test+CHANGELOG, green, review clean; CHANGELOG line conflicts with #43's, rebase whichever merges second)
