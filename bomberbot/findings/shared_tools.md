# Findings: shared_tools

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-12 | develop 1e8ae43; bundle-audit clean (fresh resolve, advisory db 4d71b12); read lib/ for shell/eval/path/SQL/SSRF/option injection; F-20..F-25, S-1..S-11 (details held locally) |
| dead code | 2026-11-13 | develop 337219d; debride 1.15.2 on lib+bin and lib+test (tool_name hits are the RubyLLM hook); per-file private-method and constant scans; RuboCop dead-code Lint cops; ruby -wc; every shim and aggregator required in a child process; SimpleCov zero-execution method list (224, mostly untested drivers, traced to live callers); aia develop c04ce8e dynamic respond_to? calls checked; F-68..F-77, S-13 |
| dependencies | 2026-11-06 | `bundle outdated --only-explicit` clean; Ruby floor checked against syntax used; findings F-8, F-9 |
| adoption | 2026-11-12 | develop 1e8ae43; `mkdocs build --strict` passes (5 orphan pages, 1 bad anchor); README/docs execute() kwargs checked by script; 31 demos run to the first LLM call; rubygems versions vs tags vs CHANGELOG; Ruby 4.1-preview1 suite with -W:deprecated clean; F-36..F-48 |
| quality | 2026-11-13 | develop 337219d; partial: CronTool, CalculatorTool, JsonQueryTool, DataPath read by hand; `rubocop --only Lint` on lib (default config, no repo .rubocop.yml): 120 offenses; flog/flay/reek not run; next run starts with composite, DNS, workflow, error-handling, TOML parser; F-59..F-64 |

Dead-code scan last covered commit: 337219d (develop, 2026-11-13, second visit: full dead-code audit, F-68..F-77, S-13). Earlier the same day 337219d (improver re-read, new F-66, F-67). Before that 1e8ae43 (develop, 2026-11-12; the improver verified two dead root scripts, a stale examples config pair, two dead Zeitwerk ignore lines, and F-5) Re-read at 1e8ae43 on the 2026-11-12 second visit (develop unmoved); new dead-code items F-57, F-58.

## Findings

### 2026-11-06 (cheap-source scan + dependencies audit)

- **F-1: browser tools never load ferrum/watir under `load_all_tools` (issue #3).** They checked `defined?(Ferrum)`/`defined?(Watir)`, but only `tools/browser.rb` requires those gems, and Zeitwerk ignores that file. `BrowserTool` was also Watir-only. Status: merged ([#5](https://github.com/MadBomber/shared_tools/pull/5), 2026-11-07).
- **F-2: `tool_classes_test.rb#test_no_ruby_llm_1x_dsl_left_in_lib` errors when no UTF-8 locale is set.** `File.read(path)` without `encoding:` raises `ArgumentError: invalid byte sequence in US-ASCII` when `LANG` is unset, as it is under launchd and bomberbot. It passes with `LANG=en_US.UTF-8`. Fix: `File.read(path, encoding: "UTF-8")`. Status: merged ([#7](https://github.com/MadBomber/shared_tools/pull/7), 2026-11-07).
- **F-3: `ComputerTool#default_driver` checks `defined?(MacOS)` without requiring the gem** (`lib/shared_tools/tools/computer_tool.rb:205`). It is the same defect as F-1: only the Zeitwerk-ignored `tools/computer.rb` requires `macos`, so under aia ComputerTool raises `LoadError` with the gem installed. Status: merged ([#6](https://github.com/MadBomber/shared_tools/pull/6), 2026-11-07).
- **F-4: browser tools raise `LoadError` (a `ScriptError`) when no driver gem is present.** RubyLLM's tool-call handler rescues only `StandardError`, so the error ends the chat turn instead of going back to the model. The `Browser::*` constructors also raise right away, so instantiating every tool fails. This is pre-existing and unchanged by #5. Changing the exception class touches public behavior. Status: needs Dewayne's decision.
- **F-5: `SharedTools::Tools::Browser::BaseTool` looks unused.** It has no subclasses in lib/ or test/, and both drivers define their own `wait_for_element`. It is a public constant, so removing it needs a deprecation. Status: needs Dewayne's decision (verified unused 2026-11-12: no subclass or reference in lib, test, examples, docs, README, or aia develop/main; never subclassed in history; options: delete with a CHANGELOG Removed line, deprecate then delete, or keep).
- **F-6: no test CI.** The only workflow is the docs deploy, so PRs get no checks. Status: PR'd ([#9](https://github.com/MadBomber/shared_tools/pull/9)).
- **F-7: generated SimpleCov output is committed under `test/coverage/`.** The `.gitignore` covers only `/coverage/`. Root cause, found 2026-11-07 (third run): SimpleCov's root was not pinned, so runs from inside `test/` wrote there and measured 0 lines. Status: PR'd ([#8](https://github.com/MadBomber/shared_tools/pull/8)).
- **F-8: the gemspec adds `bigdecimal >= 4.0` only when `RUBY_VERSION >= "4.0"`.** A gemspec is evaluated at `gem build` time, so the released 0.7.1 lists `bigdecimal >= 4.0` for every Ruby, 3.3 included. It is harmless today, but the condition does not do what it reads as. Options: make the dependency unconditional, or move the pin to the Gemfile. Status: needs Dewayne's decision.
- **F-9: `test/shared_tools/tool_classes_test.rb:34` uses the `it` block parameter (Ruby 3.4+), but the gemspec floor is `>= 3.3.0`.** It affects the tests only, not lib/. Status: merged ([#7](https://github.com/MadBomber/shared_tools/pull/7), 2026-11-07).

### 2026-11-07 (review leftovers from #6; no audit, backlog was workable)

All of these are in `lib/shared_tools/tools/computer/mac_driver.rb` and were already there before #6. #6 makes `MacDriver` reachable under `load_all_tools`, so they now matter.

- **F-10: `MacDriver` referred to a bare `Library::CoreGraphics`, so `hold_key` and `scroll` raised `NameError`.** Status: merged ([#6](https://github.com/MadBomber/shared_tools/pull/6), 2026-11-07).
- **F-11: `scroll` does not check the event pointer for NULL before `CGEventPost`/`CFRelease`, and the release is not in an `ensure`.** If CoreGraphics returns NULL, `CFRelease(NULL)` could crash the process. Status: unverified (from reading the code).
- **F-12: `scroll` returns `success: true` for an unknown direction or a nil/zero amount.** It posts a zero-delta event instead of rejecting the input. Status: open.
- **F-13: `scroll` attaches `CGEventCreateScrollWheelEvent2` to the `macos` gem's own `MacOS::Library::CoreGraphics` module at call time.** The `respond_to?`/attach check is not thread-safe. The attach would be better done once, in a module SharedTools owns. Status: open.
- **F-14: `hold_key` edge cases.**
  - The `+` key can't be held: `"cmd++"` holds only Command.
  - Modifiers are sent as flags on the main key's events, not as their own key-down events.
  - `duration` from the LLM has no bound or sign check.
  - An interrupt during the gem's own `key_down` sleep still leaves the key held.
  - `key` and `hold_key` parse combos differently (`MacOS::Keyboard#keys` downcases the key). A shared private `parse_combo` would fix that, but a plain strip breaks the literal `" "`, `"\t"` and `"\n"` keys (#6 round 3).

  Status: open (judgment; small items, maybe one PR).

### 2026-11-07, second run (review leftovers from #7; no audit, backlog was workable)

- **F-15: several lib file reads depend on the locale.** They call `File.read`/`File.readlines` with no `encoding:`, so under `LANG` unset (US-ASCII default) UTF-8 user files come back tagged as invalid strings, and later regex or JSON work could raise. Examples: `composite_analysis_tool.rb:212,214`, `replace_in_files_tool.rb:65,85`, `disk/local_driver.rb:56,64`, `process_registry.rb:120`, `workflow_manager_tool.rb:291,329`. Status: unverified (from reading the code; no failing case built yet).
- **F-16: the DSL guard regex in `tool_classes_test.rb` (`^\s*(params\s+do\b|param\s+:)`) also matches such lines inside heredoc descriptions or comments.** It could fail on a tool with no 1.x DSL left. Status: open (low; no false positive today).

### 2026-11-07, third run (review leftovers from #8; no audit: F-6, F-7, F-12 were verified open, and the last audit was under 30 days old)

- **F-17: `minimum_coverage 20` in `test/test_helper.rb` is far below what the docs claim.** README.md:24 and README.md:771 say "85%+", and `docs/development/testing.md` and `contributing.md` say 80% is "enforced by SimpleCov". The full suite measures about 81%. Options: raise the gate to about 80, or change the docs to match 20. Status: needs Dewayne's decision.
- **F-18: `docs/development/testing.md` (around line 40) shows an RSpec-era `spec_helper` with `minimum_coverage 80`.** The suite is Minitest with `test/test_helper.rb`. Status: open (adoption; a docs page, so a PR needs before/after screenshots).
- **F-19: the README says `COVERAGE=true bundle exec rake test`, but `test_helper.rb` starts SimpleCov on every run.** Status: PR'd ([#13](https://github.com/MadBomber/shared_tools/pull/13)).

### 2026-11-12 (security audit, develop 1e8ae43; improver and triage leftovers)

- **F-20: `Git::CheckoutTool` runs `git checkout <ref>` with no `--`, so a ref that names a tracked file discards that file's uncommitted changes.** `REF_RE` accepts `notes.txt`; `git/checkout_tool.rb:46-48` builds `["checkout", ref]`. In a scratch repo, checking out `notes.txt` printed "Updated 1 path from the index" and the uncommitted line was gone. Fix: add `"--"` after the ref. Status: issue [#10](https://github.com/MadBomber/shared_tools/issues/10). Dewayne opened his own fix, [#18](https://github.com/MadBomber/shared_tools/pull/18) (2026-11-13).
- **F-21: `UrlGuard` resolves `uri.host`, which keeps the brackets on IPv6 literals, so every IPv6-literal URL fails with "could not resolve host: [::1]".** Fails closed. `url_guard.rb:58`. Fix: use `uri.hostname`, landing with or after S-7. Status: open (blocked on S-7).
- **F-22: the eval tools have no wall-clock timeout and two have cleanup gaps.** `ShellEvalTool` and `PythonEvalTool` use `Open3.capture3` with no timeout (`eval/shell_eval_tool.rb:59`, `eval/python_eval_tool.rb:69`); `PythonEvalTool` unlinks its tempfile only on success (no `ensure`); `RubyEvalTool` swaps global `$stdout`. Fix: route through `ProcessRunner.capture`, `ensure` cleanup. Status: open (from reading).
- **F-23: `Docker::ComposeRunTool` uses process-global `Dir.chdir(@root) { ... }`** (`docker/compose_run_tool.rb:54`). Fix: `chdir: @root` on `Open3.capture2e`. Status: open.
- **F-24: `Disk::BaseDriver` declares `directroy_delete` (typo) and has no `directory_list`,** so the abstract contract doesn't match `LocalDriver`. `disk/base_driver.rb:21`. Status: open.
- **F-25: `ProcessStartTool#sanitize_args` checks for a raw NUL byte embedded in the source** (`process_start_tool.rb:86`); BashTool uses `0.chr`. Status: PR [#28](https://github.com/MadBomber/shared_tools/pull/28); widened 2026-11-12: `search_codebase_tool.rb:216` has a second raw NUL, and `file` reports both files as `data`, so `git diff` shows them as binary and macOS `grep` skips them.
- **F-26: two broken scripts at the repo root.** `test_gem_conflicts.rb` (`NoMethodError: check_gem_conflicts`) and `test_version_detection.rb` (`NoMethodError: load_ruby_llm_tools`) probe APIs dropped in 0.3.0; not in spec.files, not run by the Rakefile, unreferenced in the repo and aia. Status: issue [#11](https://github.com/MadBomber/shared_tools/issues/11). Dewayne opened his own fix, [#19](https://github.com/MadBomber/shared_tools/pull/19) (2026-11-13).
- **F-27: `examples/ruby_llm_config.rb` and `examples/ruby_llm_config_test.rb` are stale.** The test fails with `cannot load such file -- amazing_print`; `examples/common.rb` replaced them; nothing references them. Status: issue [#11](https://github.com/MadBomber/shared_tools/issues/11). Dewayne opened his own fix, [#19](https://github.com/MadBomber/shared_tools/pull/19) (2026-11-13).
- **F-28: `lib/shared_tools.rb:10` and `:22` tell Zeitwerk to ignore `shared_tools/ruby_llm.rb` and `tools/incomplete`, neither of which exists.** Status: PR [#24](https://github.com/MadBomber/shared_tools/pull/24).
- **F-29: wrong kwargs in docs pages.** `docs/tools/index.md:47` uses EvalTool `language:` (needs `action:`); `docs/tools/computer.md:44,209` pass `num_clicks:`, which `ComputerTool#execute` doesn't accept (use `mouse_double_click`/`mouse_triple_click`). Docs deploy on develop. Status: issue [#12](https://github.com/MadBomber/shared_tools/issues/12). Dewayne opened his own fix, [#22](https://github.com/MadBomber/shared_tools/pull/22) (2026-11-13).
- **F-30: CHANGELOG has no headings for released 0.5.0, 0.6.0, 0.7.0, 0.7.1;** all of it sits under `## Unreleased` with #5-#8, and `tool_name` (0.7.0) is never mentioned. Splitting writes version-stamped history. Status: needs Dewayne's decision (or do it at the 0.7.2 release).
- **F-31: template tools are offered to the model as real tools.** `SecureToolTemplate` and `ErrorHandlingTool` are eager-loaded `RubyLLM::Tool` subclasses, so aia sends them to the LLM. Moving them breaks public constants. Status: needs Dewayne's decision.
- S-1: security finding, details held locally. Status: PR'd this visit (link held locally).
- S-2: security finding, details held locally. Status: open.
- S-3: security finding, details held locally. Status: needs Dewayne's decision.
- S-4: security finding, details held locally. Status: needs Dewayne's decision.
- S-5: security finding, details held locally. Status: open.
- S-6: security finding, details held locally. Status: unverified.
- S-7: security finding, details held locally. Status: open.
- S-8: security finding, details held locally. Status: unverified.
- S-9: security finding, details held locally. Status: open.
- S-10: security finding, details held locally. Status: unverified.
- S-11: security finding, details held locally. Status: needs Dewayne's decision.
- **F-32: seven `Browser::*` sub-tools start headless Chrome in `#initialize`** (ClickTool, VisitTool, InspectTool, PageInspectTool, SelectorInspectTool, PageScreenshotTool, TextFieldAreaSetTool build a FerrumDriver on `new`). `ToolClassesTest#instance_of` builds each twice and never closes them (~14 Chrome processes per run), which is why CI on 3.3/3.4 needed `FERRUM_PROCESS_TIMEOUT=30` (#9). `BrowserTool` already defers driver choice to `execute`. Status: open.
- **F-33: ClipboardTool tests fail instead of skipping when no clipboard program exists.** Their guard is `rescue => e; skip`, but the tool returns `{success: false}` rather than raising. Fix: skip when `write_result[:success]` is false. Status: issue [#17](https://github.com/MadBomber/shared_tools/issues/17). Dewayne opened his own fix, [#21](https://github.com/MadBomber/shared_tools/pull/21) (2026-11-13).
- **F-34: the root-confinement check is copied six times with small differences** (Disk::BaseDriver, MultiEditTool, DownloadFileTool, ApplyPatchTool, ToolchainHelpers#jail_relative, Git::Helpers#repo_relative). One shared helper would keep them consistent. Status: open (judgment; best after #14).
- **F-35: README says `# or pg, mysql2, etc.`** for DatabaseTool, which ships only sqlite and postgres drivers; README "Safe code evaluation" for EvalTool is held with S-3/S-4. Status: open (deferred from #13 review).

### 2026-11-12, second run (adoption audit, develop 1e8ae43; improver leftovers)

- **F-36: docs teach the removed ruby_llm 1.x `param :x, desc:` DSL** (`docs/api/tool-base.md`, `api/facade-pattern.md`, `development/index.md`, `development/contributing.md`, `development/architecture.md`, `development/changelog.md`); raises `NoMethodError` on ruby_llm 2.2.0. Status: issue [#16](https://github.com/MadBomber/shared_tools/issues/16). Dewayne opened his own fix, [#23](https://github.com/MadBomber/shared_tools/pull/23) (2026-11-13).
- **F-37: docs show `RubyLLM::Agent.new(tools: [...])` and `agent.process`** (`docs/tools/calculator.md`, `weather.md`, `guides/testing.md`); `ArgumentError: unknown keyword: :tools`. Status: issue [#16](https://github.com/MadBomber/shared_tools/issues/16). Dewayne opened his own fix, [#23](https://github.com/MadBomber/shared_tools/pull/23) (2026-11-13).
- **F-38: 13 doc links point at `examples/*_example.rb` files renamed to `*_demo.rb`** (`docs/tools/{browser,disk,database,eval}.md`, `docs/examples/*.md:11`, `docs/examples/workflows.md:7`). Status: open (docs-only PR; docs deploy from develop).
- **F-39: `docs/tools/index.md:276` CronTool example lacks `action:`** and raises. Status: added to issue [#12](https://github.com/MadBomber/shared_tools/issues/12) by comment.
- **F-40: docs site covers about 20 of 48 tools; 5 existing pages are not in the `mkdocs.yml` nav** (calculator, data_science_kit, dns_tool, notification_tool, weather). Status: nav fix open (touches mkdocs.yml, never low risk); coverage of the other 28 tools needs Dewayne's decision.
- **F-41: correction to F-30.** v0.5.0 was tagged but never published; `[0.4.0]` has no tag or release; published 0.2.3, 0.3.0, 0.3.1 have no heading; the `## Released` divider sits below 0.4.4 and 0.4.2. Status: needs Dewayne's decision (with F-30, at the next release).
- **F-42: `docs/development/changelog.md` is a separate stale changelog** (newest `[0.2.1]`) with a migration guide showing APIs that never existed. Status: needs Dewayne's decision (include `CHANGELOG.md` or link it).
- **F-43: no `llms.txt`.** Status: open (adoption; docs deploy from develop, so not self-mergeable).
- **F-44: gemspec metadata lacks `documentation_uri`, `bug_tracker_uri`, `rubygems_mfa_required`; summary is stale** ("...with configurable logging"). Status: URIs open (gemspec, never low risk); MFA and wording need Dewayne's decision.
- **F-45: `examples/common.rb:7,21` and `examples/README.md` say the default is Ollama and document `OLLAMA_HOST`;** `configure!` defaults to `claude-haiku-4-5` and reads `OLLAMA_URL`. Status: open (examples/docs only).
- **F-46: `docs/tools/computer.md` links to a missing anchor `examples/index.md#computer-tool-example`.** Status: open (fold with F-38).
- **F-47: `DataScienceKit` declares a `confidence_level` parameter it never uses** (`data_science_kit.rb:55`, assigned at 275); `n_features` at 643 is also unused. Status: needs Dewayne's decision (use it or drop it).
- **F-48: `DataScienceKit` median and quartiles are wrong for an even row count** (`data_science_kit.rb:297-303`, index pick, no averaging). Status: issue [#15](https://github.com/MadBomber/shared_tools/issues/15). Dewayne opened his own fix, [#20](https://github.com/MadBomber/shared_tools/pull/20) (2026-11-13).
- **F-50: clipboard tests overwrite the developer's real clipboard and never restore it.** Status: issue [#17](https://github.com/MadBomber/shared_tools/issues/17) (with F-33). Dewayne opened his own fix, [#21](https://github.com/MadBomber/shared_tools/pull/21) (2026-11-13).
- **F-51: the confirmation prompt prints `(y/N` with no closing parenthesis** (`lib/shared_tools.rb:51`); every docs page shows `(y/N)`. Status: open (1 lib line + test).
- **F-52: ErrorHandlingTool retry tests sleep for real** (about 9 s of a 27 s suite; `error_handling_tool.rb:208`). Stubbing `sleep` works. Status: open (test-only).
- **F-53: RubyLLM INFO logging is 1350 of the suite's 1603 output lines.** Set `log_level` to FATAL in `test_helper.rb`. Status: open (test-only).
- **F-54: `SharedTools.tools` returns every `RubyLLM::Tool` subclass in the process,** not only this gem's (`lib/shared_tools.rb:67`); aia doesn't call it. Status: open (changes a public return value toward its docs; flag in the PR).
- **F-55: `notification_tool` and `search_codebase_tool` have no `lib/shared_tools/<name>_tool.rb` require shim;** `require "shared_tools/notification_tool"` raises LoadError. Status: PR [#29](https://github.com/MadBomber/shared_tools/pull/29).
- **F-56: `disk/base_driver.rb` and `browser/base_driver.rb` raise `"Object undefined"` with no `#` separator.** Status: open (Disk part after #14 merges; combine with F-24).
- **F-57: `Tools::Enabler` (`lib/shared_tools/tools/enabler.rb`) is experimental, Zeitwerk-ignored, defines top-level `::Tools`, and is shipped and unreferenced** (lib, test, docs, aia). Status: needs Dewayne's decision (delete, move under SharedTools, or turn into an issue).
- **F-58: `Browser::Formatters::ElementFormatter.format_single_element`/`format_element_attributes` are uncalled** ("backward compatibility" comment; no callers in repo or aia). Status: needs Dewayne's decision (remove with F-5 or keep).
- Minor, unfiled: `eval/python_eval_tool.rb:97` requires the bundled gem `base64` (`pack("m0")` needs no require); `test_helper.rb:6-10` triggers four SimpleCov deprecation warnings.

### 2026-11-13 (quality audit, partial, develop 337219d; improver leftovers)

- **F-59: `CronTool#next_times` fails during minute :59** (`cron_tool.rb:121`, `Time.new(..., t.min + 1, 0)` raises "min out of range"); the cron test doesn't pin the clock, so the suite also fails about 1 run in 60. Status: issue [#25](https://github.com/MadBomber/shared_tools/issues/25).
- **F-60: `CronTool` reads non-numeric fields as 0** (`expand_field`, `String#to_i`; `0 9 * * MON` validates as "on Sunday"). Status: issue [#26](https://github.com/MadBomber/shared_tools/issues/26).
- **F-61: `CronTool` doesn't bounds-check stepped ranges and accepts reversed ranges** (`expand_range_part`, `cron_tool.rb:257-264`). Status: issue [#26](https://github.com/MadBomber/shared_tools/issues/26).
- **F-62: `CronTool#next_times` ANDs day-of-month and day-of-week;** standard cron ORs them when both are restricted (`cron_tool.rb:126-130`). Status: issue [#26](https://github.com/MadBomber/shared_tools/issues/26).
- **F-63: `CronTool` `generate` ignores the time in "every weekday at 8am"** (returns `0 9 * * 1-5`; `cron_tool.rb:178-179`). Status: open.
- **F-64: `rescue LoadError, Gem::LoadError` is redundant** (`calculator_tool.rb:7`, `database_query_tool.rb:7`, `process_registry.rb:149`); the repo has no `.rubocop.yml`, so builders lint with RuboCop defaults (120 Lint offenses in lib). Status: open (rescues) / needs Dewayne's decision (adding a RuboCop config).
- **F-65: nine tool files start with a bare `require 'ruby_llm/tool'` and `notification_tool.rb` requires nothing, so requiring them directly raises NameError** (ruby_llm 2.2.0's `tool.rb` doesn't load `RubyLLM::Tool::Support`), though `docs/tools/weather.md` shows that require. Status: issue [#27](https://github.com/MadBomber/shared_tools/issues/27).
- **F-66: `lib/shared_tools/mcp/` holds five non-Ruby leftovers** (four Claude Code `mcpServers` JSON snippets and `playwright.md`, a pasted chat reply); not in `spec.files`, referenced only by `mcp/README.md`'s "Reference Files" list. Status: needs Dewayne's decision (he may keep them as notes); removal is ~70 lines.
- **F-67: `SharedTools.npm_install` and `gem_install` are public but called only by their own tests.** Status: needs Dewayne's decision (keep, deprecate, or move the installers under `SharedTools::MCP`).
- S-12: security finding, details held locally. Status: open.
- Verified 2026-11-13 (second visit), fix after #20 merges: `DataScienceKit#load_from_database` ignores its `query` argument (RuboCop `Lint/UnusedMethodArgument` at `data_science_kit.rb:180:30`) and has an empty rescue (`:213`).

### 2026-11-13, second visit (dead-code audit, develop 337219d; improver leftovers)

- **F-68: `DiskTool` and `ComputerTool` return `nil` for an unknown action, and their `ACTIONS` constants are never read** (`disk_tool.rb:117-128`, `computer_tool.rb:184-198`, no `else`; EvalTool/DocTool/BrowserTool/GemTool return "Unsupported action ... Supported actions are: ..."). `ComputerTool::ACTIONS` (`:46-58`) also omits `mouse_double_click` and `mouse_triple_click`. Fix: add the two actions and an `else` error built from `ACTIONS`, with tests. Status: open (obvious-yes PR).
- **F-69: `Disk::BaseTool` and `Docker::BaseTool` have never been subclassed** (added c03f8a6; every Disk tool and `Docker::ComposeRunTool` subclass `::RubyLLM::Tool`; `ComposeRunTool` duplicates `CaptureError`/`capture!`; `docker/base_tool.rb:9` doc example garbled). Same case as F-5. Status: needs Dewayne's decision (public constants: delete with CHANGELOG Removed, deprecate, or keep).
- **F-70: `WorkflowManagerTool`'s private `attr_reader :storage_dir` "(for testing)" is never read** (`workflow_manager_tool.rb:340-341`; code uses `@storage_dir`; no `send(:storage_dir)` anywhere, aia included). Fix: delete two lines. Status: open (obvious-yes removal).
- **F-71: `RubyOutline::RipperBackend` (~115 lines, `ruby_outline.rb:91-205`) is unreachable on supported Rubies** (Prism is a default gem since 3.3.0, the gemspec floor; SimpleCov shows all 9 methods unexecuted; no test passes `backend:`). Status: needs Dewayne's decision (drop it, or keep with a parity test).
- **F-72: nokogiri is a runtime dependency but three browser tools guard it as optional** (`inspect_tool.rb:3-7,32`, `selector_inspect_tool.rb:3-7,31`, `page_inspect_tool.rb:3-7,30`), while `browser/inspect_utils.rb:10` and `elements/nearby_element_detector.rb:80` use it unguarded. Status: needs Dewayne's decision (drop the dead guards, or demote nokogiri to optional like ferrum; gemspec change).
- **F-73: generated workflow state is committed:** `examples/.workflows/workflow_1a891023-*.json` and `workflow_37bcdad8-*.json` (~20 KB, added 56c02f9, before `.gitignore:27` covered the directory). Contents are step data only. Fix: `git rm --cached` both. Status: open (obvious-yes removal).
- **F-74: unused public constants:** `SharedTools::Tools::Error` (`tools.rb:14`), `ComputerTool::MOUSE_BUTTON_OPTIONS` / `SCROLL_DIRECTION_OPTIONS` (`computer_tool.rb:60,66`), `Computer::BaseDriver::DEFAULT_DISPLAY_SCALE` (`computer/base_driver.rb:69`). Alternative: use the two `*_OPTIONS` lists to validate input (pairs with F-12). Status: needs Dewayne's decision.
- **F-75: `database_tool.rb:77` `@example` calls `SharedTools::Tools::Database::BaseTool.new`, which doesn't exist.** Fix: `DatabaseTool.new(driver: ...)`. Status: open (tiny; fold into another Database PR).
- **F-76: unused method parameters:** `composite_analysis_tool.rb:247` `generate_insights` ignores `options`; `:430` `assess_data_quality` ignores `data`; `notification/linux_driver.rb:77` `alert_zenity` ignores `default_button`. Status: open (low).
- **F-77: on macOS, NotificationTool `notify`/`alert` fail for text containing `#{`, `#$` or `#@`** (`notification/mac_driver.rb:16-19,30-34` quote AppleScript with `String#inspect`, which writes `\#{`; `osascript -e 'return "x\#{y}"'` => syntax error -2741). Fix: an AppleScript-quote helper that escapes only `\` and `"`, with an `Open3`-stubbed test. Status: open (obvious-yes PR).
- **F-78: `CompositeAnalysisTool` returns simulated data as if it were the real source** (`fetch_web_data` never fetches; `read_file_data` substitutes samples for missing files and every `.csv`; ~38 tests depend on it). Status: issue [#30](https://github.com/MadBomber/shared_tools/issues/30).
- **F-79: `CompositeAnalysisTool` and `DataScienceKit` ignore the nested `options`/`parameters` object their schemas declare** (`execute(**options)` reads top-level keys). Status: issue [#31](https://github.com/MadBomber/shared_tools/issues/31) (DataScienceKit half after #20).
- **F-80: `WorkflowManagerTool` creates `.workflows/` in the cwd on `new`** (`workflow_manager_tool.rb:70`; aia instantiates every tool). Status: issue [#32](https://github.com/MadBomber/shared_tools/issues/32).
- **F-81: `TomlParser` rejects TOML 1.0 multiline strings with 1-2 quotes before the closing delimiter, and doesn't trim CRLF after an opening `"""`/`'''`** (lines 237, 260; spec example `str7` raises). `a = -0x10` parses as 16. Status: open (~15 lines plus a new `toml_parser_test.rb`).
- **F-82: `DnsTool#whois_query` has no read timeout** (`dns_tool.rb:313`; `connect_timeout` only; a silent server blocks past `WHOIS_TIMEOUT`). Fix: `sock.timeout = WHOIS_TIMEOUT` (Ruby 3.2+). Status: open (obvious-yes PR).
- **F-83: `CompositeAnalysisTool#calculate_statistics` median uses the upper middle element** (same bug as F-48, different file). Status: open; match #20's approach after it merges.
- **F-84: `DnsTool` host-based actions with `host: nil` return resolver errors instead of "host is required"** (`whois` already does). Status: open (low).
- **F-85: `WorkflowManagerTool` wraps `step_data`** (`execute(**step_data)` stores `{data: {step_data: {...}}}`; `input_keys` always `[:step_data]`). Unwrapping changes saved JSON. Status: needs Dewayne's decision (unwrap and read both shapes, unwrap with a CHANGELOG note, or leave).
- **F-86: `DnsTool#http_get` drops the query string** (`uri.path`, not `uri.request_uri`); harmless today. Status: open (low).
- Minor, unfiled: `minitest-mock` in both `Gemfile:10` and the gemspec; `private` before constants in `cron_tool.rb:71-80` does nothing; `Browser::BaseTool`'s `defined?(RSpec)` branch is dead (goes with F-5); `Browser::BaseDriver::TIMEOUT` reads legacy `OMNIAI_BROWSER_TIMEOUT`; `database_tool_test.rb:93` `executed_count` never asserted.
- S-13: security finding, details held locally. Status: open.
