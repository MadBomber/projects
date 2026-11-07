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

Dead-code scan last covered commit: b2e6422 (develop, 2026-11-07; quick `debride` over the browser files changed by #5, nothing new)

## Findings

### 2026-11-06 (cheap-source scan + dependencies audit)

- **F-1: browser tools never load ferrum/watir under `load_all_tools` (issue #3).** They checked `defined?(Ferrum)`/`defined?(Watir)`, but only `tools/browser.rb` requires those gems, and Zeitwerk ignores that file. `BrowserTool` was also Watir-only. Status: merged ([#5](https://github.com/MadBomber/shared_tools/pull/5), 2026-11-07).
- **F-2: `tool_classes_test.rb#test_no_ruby_llm_1x_dsl_left_in_lib` errors when no UTF-8 locale is set.** `File.read(path)` without `encoding:` raises `ArgumentError: invalid byte sequence in US-ASCII` when `LANG` is unset, as it is under launchd and bomberbot. It passes with `LANG=en_US.UTF-8`. Fix: `File.read(path, encoding: "UTF-8")`. Status: open.
- **F-3: `ComputerTool#default_driver` checks `defined?(MacOS)` without requiring the gem** (`lib/shared_tools/tools/computer_tool.rb:205`). It is the same defect as F-1: only the Zeitwerk-ignored `tools/computer.rb` requires `macos`, so under aia ComputerTool raises `LoadError` with the gem installed. Status: PR'd ([#6](https://github.com/MadBomber/shared_tools/pull/6)).
- **F-4: browser tools raise `LoadError` (a `ScriptError`) when no driver gem is present.** RubyLLM's tool-call handler rescues only `StandardError`, so the error ends the chat turn instead of going back to the model. The `Browser::*` constructors also raise right away, so instantiating every tool fails. This is pre-existing and unchanged by #5. Changing the exception class touches public behavior. Status: needs Dewayne's decision.
- **F-5: `SharedTools::Tools::Browser::BaseTool` looks unused.** It has no subclasses in lib/ or test/, and both drivers define their own `wait_for_element`. It is a public constant, so removing it needs a deprecation. Status: unverified (not yet checked in aia or examples/).
- **F-6: no test CI.** The only workflow is the docs deploy, so PRs get no checks. Status: open (adding a test workflow is a candidate PR).
- **F-7: generated SimpleCov output is committed under `test/coverage/`.** The `.gitignore` covers only `/coverage/`. Status: open.
- **F-8: the gemspec adds `bigdecimal >= 4.0` only when `RUBY_VERSION >= "4.0"`.** A gemspec is evaluated at `gem build` time, so the released 0.7.1 lists `bigdecimal >= 4.0` for every Ruby, 3.3 included. It is harmless today, but the condition does not do what it reads as. Options: make the dependency unconditional, or move the pin to the Gemfile. Status: needs Dewayne's decision.
- **F-9: `test/shared_tools/tool_classes_test.rb:34` uses the `it` block parameter (Ruby 3.4+), but the gemspec floor is `>= 3.3.0`.** It affects the tests only, not lib/. Status: open (trivial).

### 2026-11-07 (review leftovers from #6; no audit, backlog was workable)

All of these are in `lib/shared_tools/tools/computer/mac_driver.rb` and were already there before #6. #6 makes `MacDriver` reachable under `load_all_tools`, so they now matter.

- **F-10: `MacDriver` referred to a bare `Library::CoreGraphics`, so `hold_key` and `scroll` raised `NameError`.** Status: PR'd ([#6](https://github.com/MadBomber/shared_tools/pull/6)).
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
