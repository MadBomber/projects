# Findings: shared_tools

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | 2026-11-06 | `bundle outdated --only-explicit` clean; Ruby floor checked against syntax used; findings F-8, F-9 |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: 4ca4469 (develop, 2026-11-06; quick `debride lib` pass)

## Findings

### 2026-11-06 (cheap-source scan + dependencies audit)

- **F-1: browser tools never load ferrum/watir under `load_all_tools` (issue #3).** They checked `defined?(Ferrum)`/`defined?(Watir)`, but only `tools/browser.rb` requires those gems, and Zeitwerk ignores that file. `BrowserTool` was also Watir-only. Status: PR'd ([#5](https://github.com/MadBomber/shared_tools/pull/5)).
- **F-2: `tool_classes_test.rb#test_no_ruby_llm_1x_dsl_left_in_lib` errors when no UTF-8 locale is set.** `File.read(path)` without `encoding:` raises `ArgumentError: invalid byte sequence in US-ASCII` when `LANG` is unset, as it is under launchd and bomberbot. It passes with `LANG=en_US.UTF-8`. Fix: `File.read(path, encoding: "UTF-8")`. Status: open.
- **F-3: `ComputerTool#default_driver` checks `defined?(MacOS)` without requiring the gem** (`lib/shared_tools/tools/computer_tool.rb:205`). It is the same defect as F-1: only the Zeitwerk-ignored `tools/computer.rb` requires `macos`, so under aia ComputerTool raises `LoadError` with the gem installed. Status: open.
- **F-4: browser tools raise `LoadError` (a `ScriptError`) when no driver gem is present.** RubyLLM's tool-call handler rescues only `StandardError`, so the error ends the chat turn instead of going back to the model. The `Browser::*` constructors also raise right away, so instantiating every tool fails. This is pre-existing and unchanged by #5. Changing the exception class touches public behavior. Status: needs Dewayne's decision.
- **F-5: `SharedTools::Tools::Browser::BaseTool` looks unused.** It has no subclasses in lib/ or test/, and both drivers define their own `wait_for_element`. It is a public constant, so removing it needs a deprecation. Status: unverified (not yet checked in aia or examples/).
- **F-6: no test CI.** The only workflow is the docs deploy, so PRs get no checks. Status: open (adding a test workflow is a candidate PR).
- **F-7: generated SimpleCov output is committed under `test/coverage/`.** The `.gitignore` covers only `/coverage/`. Status: open.
- **F-8: the gemspec adds `bigdecimal >= 4.0` only when `RUBY_VERSION >= "4.0"`.** A gemspec is evaluated at `gem build` time, so the released 0.7.1 lists `bigdecimal >= 4.0` for every Ruby, 3.3 included. It is harmless today, but the condition does not do what it reads as. Options: make the dependency unconditional, or move the pin to the Gemfile. Status: needs Dewayne's decision.
- **F-9: `test/shared_tools/tool_classes_test.rb:34` uses the `it` block parameter (Ruby 3.4+), but the gemspec floor is `>= 3.3.0`.** It affects the tests only, not lib/. Status: open (trivial).
