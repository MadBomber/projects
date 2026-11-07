# Findings: aia

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | 2026-11-06 | bundle outdated clean; bundle-audit clean; no Ruby 4 removed APIs in lib/bin; suite hermeticity issues found |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: (none; first run spent on dependencies audit, develop at e5c7f1c)

## Findings

### 2026-11-06 dependencies audit (develop e5c7f1c)

- **F-1** (PR'd: https://github.com/MadBomber/aia/pull/157): 37 unit tests in `network_builder_test.rb` and `robot_factory_network_test.rb` error with `RubyLLM::ConfigurationError` when `OPENAI_API_KEY` is unset (ruby_llm 2.1.1 `Provider#ensure_configured!` on chat creation).
- **F-2** (PR'd: https://github.com/MadBomber/aia/pull/157): `VersionTest#test_version_consistency_with_gemspec` raises under a US-ASCII locale and its assertion compared `AIA::VERSION` with itself.
- **F-3** (PR'd: https://github.com/MadBomber/aia/pull/157): 43 test files use `OpenStruct` without requiring `ostruct`; they fail when run on their own.
- **F-4** (open): 6 unit test files fail when run on their own with `uninitialized constant` (`AIA::TaskDecomposer`, `AIA::TaskExecutor`, `AIA::ToolFilter::TFIDF`, `AIA::TaskBoardTool`): `task_decomposer_test.rb`, `task_executor_test.rb`, `tool_filter/fingerprint_test.rb`, `tool_filter/wordnet_expansion_wiring_test.rb`, `tool_filter_registry_test.rb`, `tools/task_board_tool_test.rb`. Each needs the right require; obvious-yes follow-up PR.
- **F-5** (open): about 33 test files keep a per-file `require 'ostruct'` that is redundant once #157 lands. Low-value cleanup; fold into F-4 only if it stays small.
- **F-6** (needs Dewayne's decision): no GitHub Actions workflow runs the test suite; the only workflow deploys docs. Options: add a minimal `rake test` workflow on Ruby 4.0 (Ruby 4.1 is preview), or keep testing local-only.
- **F-7** (needs Dewayne's decision): Dependabot alerts are disabled on the repo (API returns 403). Enabling is a repo setting only Dewayne can change.
- **F-8** (unverified): `.rubocop.yml` inherits `../.rubocop-base.yml`, which exists only in the `robot_lab_project` workspace, so rubocop fails in a fresh clone.
- **F-9** (unverified): issue #134 (`--speak`). The `AiClient` references are gone from lib on develop; the remaining speak stubs and duplicate speak calls need re-checking. Needs a design decision on TTS approach before any PR.
- **S-1** (needs Dewayne's decision): security finding, details held locally.
