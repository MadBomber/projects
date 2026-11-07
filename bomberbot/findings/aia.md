# Findings: aia

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | 2026-11-07 | debride over lib and bin, then hand checks; no removal PR candidates, results recorded as F-12 and F-13 |
| dependencies | 2026-11-06 | bundle outdated clean; bundle-audit clean; no Ruby 4 removed APIs in lib/bin; suite hermeticity issues found |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: 994ce0c (develop, 2026-11-07 third run; ae4719a..994ce0c changed only tests and CHANGELOG, nothing new)

## Findings

### 2026-11-06 dependencies audit (develop e5c7f1c)

- **F-1** (merged: https://github.com/MadBomber/aia/pull/157): 37 unit tests in `network_builder_test.rb` and `robot_factory_network_test.rb` error with `RubyLLM::ConfigurationError` when `OPENAI_API_KEY` is unset (ruby_llm 2.1.1 `Provider#ensure_configured!` on chat creation).
- **F-2** (merged: https://github.com/MadBomber/aia/pull/157): `VersionTest#test_version_consistency_with_gemspec` raises under a US-ASCII locale and its assertion compared `AIA::VERSION` with itself.
- **F-3** (merged: https://github.com/MadBomber/aia/pull/157): 43 test files use `OpenStruct` without requiring `ostruct`; they fail when run on their own.
- **F-4** (PR'd: https://github.com/MadBomber/aia/pull/158; verified 2026-11-07, exactly these 6 of 122 unit files): 6 unit test files fail when run on their own with `uninitialized constant` (`AIA::TaskDecomposer`, `AIA::TaskExecutor`, `AIA::ToolFilter::TFIDF`, `AIA::TaskBoardTool`): `task_decomposer_test.rb`, `task_executor_test.rb`, `tool_filter/fingerprint_test.rb`, `tool_filter/wordnet_expansion_wiring_test.rb`, `tool_filter_registry_test.rb`, `tools/task_board_tool_test.rb`. Each needs the right require; obvious-yes follow-up PR.
- **F-5** (open): about 33 test files keep a per-file `require 'ostruct'` that is redundant now that #157 has merged. Low-value cleanup. The same files often keep a `require_relative '../../lib/aia'` that is also redundant, because `test_helper.rb` already runs `require "aia"`. Do both in one PR.
- **F-6** (needs Dewayne's decision): no GitHub Actions workflow runs the test suite; the only workflow deploys docs. Options: add a minimal `rake test` workflow on Ruby 4.0 (Ruby 4.1 is preview), or keep testing local-only.
- **F-7** (needs Dewayne's decision): Dependabot alerts are disabled on the repo (API returns 403). Enabling is a repo setting only Dewayne can change.
- **F-8** (open; verified 2026-11-07, `Configuration file not found: .../.rubocop-base.yml` in a fresh clone): `.rubocop.yml` inherits `../.rubocop-base.yml`, which exists only in the `robot_lab_project` workspace, so rubocop fails in a fresh clone.
- **F-9** (unverified): issue #134 (`--speak`). The `AiClient` references are gone from lib on develop; the remaining speak stubs and duplicate speak calls need re-checking. Needs a design decision on TTS approach before any PR.
- **S-1** (needs Dewayne's decision): security finding, details held locally.

### 2026-11-07 dead-code audit (develop ae4719a)

- **F-10** (PR'd: https://github.com/MadBomber/aia/pull/160; verified 2026-11-07 on 994ce0c; the PR keeps `prepare_task_board`'s `task_board_tool` require, because ToolLoader offers TaskBoardTool to every robot): `lib/aia/delegate_handler.rb:41,61` uses `TaskDecomposer` and `TaskExecutor` without requiring them, and `lib/aia/tools/delegate_to_foreman_tool.rb:53` uses `TaskBoardTool` the same way. Both work today only because their single loader (`special_mode_handler.rb:255-257`, `startup_coordinator.rb:46-47`) requires the sibling as well. Fix: have each file require its own dependencies, then drop the paired requires from the loaders and tests. Lazy loading and startup cost are unchanged. Small, obvious-yes PR. Found by `/simplify` on #158.
- **F-11** (PR'd: https://github.com/MadBomber/aia/pull/159; re-verified 2026-11-07 on ae4719a): `test/test_helper.rb:46-66` overrides `Kernel#exit`/`exit!` to keep aia's own exit calls from killing the test run, and the override also swallows Minitest's `exit(false)` at exit. A test file with errors, and `rake test` with failures, both exit 0 (confirmed on develop ae4719a with the pre-#158 `task_decomposer_test.rb`: 5 errors, exit 0). Any future CI (F-6) would report green on a red suite. Fix: let the override pass through when it is called from Minitest's at_exit, or record the status and re-raise it after Minitest reports. Prove it with a test that runs a failing file in a subprocess and checks for a non-zero exit.
- **F-12** (needs Dewayne's decision): these public module methods have no caller in lib, bin, test, docs or examples (debride plus a word-boundary grep, ae4719a): `AIA.fuzzy?`, `AIA::Utility.tools?`, `.user_tools?`, `.supports_tools?`, `.mcp_servers?`, `.connected_mcp_servers?`, `.build_crew_line`, `AIA::UIPresenter#display_error`, `#display_warning`, `AIA::LoggerManager.llm_log_level_symbol`. They are public and could be called from user prompt ERB or plugins, so removing them needs a deprecation decision. Options: deprecate in 2.1 and remove in 3.0, or declare `AIA::Utility` and `UIPresenter` internal and remove them now.
- **F-13** (unverified): these lib methods are called only by tests: `ContentExtractor#store_results_in_memory`, `NetworkBuilder#build_pipeline_network` (24 lines), `ModelAliasRegistry.resolve_multiple`, `.all_aliases`, `PromptHandler#load_role_for_model`, `SessionTracker#record_model_switch`, `#record_user_feedback`, `#to_facts`, `SpawnHandler#cleanup!`, `ToolFilter#fingerprint_from_tools`, `MCPConnectionManager#any_tools?`, `#connected_server_names`, `#failed_server_names`, `ModelSpec#duplicate?`, `PromptHandler::SHORTHAND_KEYS`. Each needs a check for dynamic callers and robot_lab callbacks before it can be a removal candidate. Some may be planned features rather than leftovers.
- Not dead (checked and ruled out): `Config.validate_environment!` (myway_config hook), `Config#ensure_arrays` (`on_load`), `Directive#build_dispatch_block` (prompt_manager override), and directive aliases (dispatched by name).

### 2026-11-07 second run (review of #159, develop ae4719a)

- **F-14** (open): Minitest's own `exit 1` for an unknown command-line option (and its exit after `--help`) runs inside `Minitest.run` while the test helper's exit guard is still on, so it is swallowed: the suite runs anyway and can exit 0. Found by `/code-review` round 2 on #159; it predates #159. Fix idea: turn the guard on only around test execution (for example a `Minitest::Test#before_setup`/`after_teardown` pair) rather than from load time. Small, but it needs a test that runs a probe with a bad option.
- **F-15** (needs Dewayne's decision): the helper's global `Kernel#exit` override makes a suppressed `exit 1` return false, so lib code keeps running past exit points in tests (see the `return # exit is mocked in tests` in `lib/aia/config.rb`). The alternative is to delete the override and have each exit-path test use `assert_raises(SystemExit)` or stub `exit` on the receiver. The catch: Minitest passes `SystemExit` through, so any unstubbed `exit(0)` would silently end the suite early with status 0. Options: keep the override (current), or remove it and audit every test that reaches one of about 24 lib exit sites.

### 2026-11-07 third run (work on #160, develop 994ce0c)

- **F-16** (needs Dewayne's decision): eleven lazy `require_relative` sites in lib (`SpecialModeHandler` loads debate_handler, delegate_handler, spawn_handler, layered_orchestrator, verification_network, prompt_decomposer; `ToolFilterRegistry` loads tool_filter/tfidf; `StartupCoordinator` loads the task-board tools). A hidden dependency between lazy files shows only when the file is loaded in a fresh process; #160 pins two of them with a child-process test. Options: `autoload` entries in `lib/aia.rb` for the lazily loaded classes, a test that requires every lazy target in its own child process, or leave it per-file. Raised by `/code-review` on #160.
- **F-17** (open): `test/simplecov_helper.rb` uses `SimpleCov.add_filter`, `track_files` and `add_group`, which the installed simplecov deprecates, so every test run prints 7 deprecation lines. Same fix as prompt_manager#21 (`skip`, `cover`, `group`), checking the coverage file list stays the same.
