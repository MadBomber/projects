# shared_tools

Local tool definitions for Ruby AI frameworks, used by aia. A change to a public method must not break aia; run aia's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/shared_tools
- RubyGems: https://rubygems.org/gems/shared_tools
- Issues: https://github.com/MadBomber/shared_tools/issues

## Status

_Updated 2026-11-12._ #8 merged; first security audit ran (S-1..S-11 held locally); three PRs open: test CI (#9), README accuracy (#13), disk path normalization (#14). `develop` is 4 merged fixes ahead of v0.7.1 and issue #3 waits on a release.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Add test CI (F-6). **PR open:** [#9](https://github.com/MadBomber/shared_tools/pull/9)
- README examples and facts match the code (F-19 and improver items). **PR open:** [#13](https://github.com/MadBomber/shared_tools/pull/13)
- Normalize disk driver paths before the root check. **PR open:** [#14](https://github.com/MadBomber/shared_tools/pull/14)
- Remove broken leftover scripts at the root and in examples/ (F-26, F-27): [issue #11](https://github.com/MadBomber/shared_tools/issues/11)
- `Git::CheckoutTool` needs `--` after the ref (F-20): [issue #10](https://github.com/MadBomber/shared_tools/issues/10)
- Docs examples for EvalTool/ComputerTool raise ArgumentError (F-29): [issue #12](https://github.com/MadBomber/shared_tools/issues/12) (docs deploy from develop)
- Open security items S-2, S-5, S-7, S-9 have small fixes (details held locally; neutral titles only)

## Merged

- [#8](https://github.com/MadBomber/shared_tools/pull/8): fix(test): pin SimpleCov root and stop tracking test/coverage (merged 2026-11-07, merge commit, no comments)
- [#7](https://github.com/MadBomber/shared_tools/pull/7): fix(test): read lib files as UTF-8 in the DSL guard test (merged 2026-11-07, no comments)
- [#6](https://github.com/MadBomber/shared_tools/pull/6): fix(computer): require macos on demand when picking a driver (merged 2026-11-07, no comments)
- [#5](https://github.com/MadBomber/shared_tools/pull/5): fix(browser): require ferrum or watir when picking a driver (merged 2026-11-07, no comments)

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/shared_tools/pull/9): ci: run the Minitest suite on push and pull request (opened 2026-11-12; green on 3.3/3.4/4.0; review hit the cap)
- [#13](https://github.com/MadBomber/shared_tools/pull/13): docs(readme): match examples and facts to the code (opened 2026-11-12; 3 deferred review notes, so not self-merged)
- [#14](https://github.com/MadBomber/shared_tools/pull/14): fix(disk): normalize paths before the root check (opened 2026-11-12; lib change; review hit the cap)
