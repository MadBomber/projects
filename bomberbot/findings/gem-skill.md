# Findings: gem-skill

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-10 | first run at 44a608c; bundle-audit clean (DB 2026-11-09) on a freshly resolved lock (no lockfile tracked); manual review of lib/, plugins.rb, bin/, scripts/; S-1..S-4 held locally |
| dead code | 2026-11-07 (quick scan, third run) | 36fcf74..5552441 (#3) touched no code under lib/, bin/ or scripts/; nothing removable |
| dependencies | never | |
| adoption | 2026-11-06 | first run; README/docs vs 0.2.2 API, CHANGELOG, local-model path exercised without network |
| quality | never | |

Dead-code scan last covered commit: 44a608c (2026-11-10 improver pass; F-28 found)

## Findings

### 2026-11-06 adoption audit (develop at 39a14c8, same as main)

- **F-1 (PR'd: [#2](https://github.com/MadBomber/gem-skill/pull/2)):** `Generator#temperature_supported?` calls `RubyLLM.models.find`, which raises `RubyLLM::ModelNotFoundError` for any `lms/` or `apfel/` model id not in the static registry. So every `apfel/...` model fails (the Apfel gem ships no models.json) and so does every LM Studio model except the five in ruby_llm-providers-lms's models.json, before any request is sent. Reproduced with `Generator#build_chat` for `lms/qwen/qwen3-4b` and `apfel/apple-foundationmodel`. With `temperature: nil` both build fine, and `Verifier#build_chat` is fine, so the lookup is the only blocker. Also, `ModelNotFoundError` is not a `RubyLLM::Error`, so `Generator#generate`'s rescue doesn't wrap it. Fix: rescue in `model_info`/`temperature_supported?` and treat unknown as supported. Add a failing test.
- **F-2 (PR'd: [#3](https://github.com/MadBomber/gem-skill/pull/3); earlier: gave up at the review cap, branch [bomber/document-local-models](https://github.com/MadBomber/gem-skill/tree/bomber/document-local-models)):** README and docs say nothing about the 0.2.2 `lms/` and `apfel/` model prefixes, `LMS_API_BASE`, `LMS_API_KEY`, `APFEL_API_BASE`, `APFEL_API_KEY`, or `GEMSKILL_REQUEST_TIMEOUT`. The README says an API key is required. `docs/installation.md` says Ruby >= 3.2.0 but the gemspec says >= 3.4. The branch fixes all of this and adds `test/docs_test.rb`, which fails when an env var read in lib/ is undocumented. Review round 3 found that the documented example hits F-1, so land F-1 first, then rebase the branch. The Apfel text also needs to cover: the 4,096-token window versus the default `GEMSKIL_MAX_TOKENS` 32767 (tell users to lower it), and the "Apple Intelligence enabled" requirement.
- **F-3 (PR'd: [#10](https://github.com/MadBomber/gem-skill/pull/10)):** In `BundlerCommand.parse_options`, a flag that takes a value breaks when placed before the subcommand. `bundle skill --model X install` gives "unknown subcommand X", because the value lands in `remaining`, twice when `--model` is set. Same for `--max-tokens N install`. With the subcommand first it works, as every doc example does.
- **F-4 (PR'd: [#10](https://github.com/MadBomber/gem-skill/pull/10)):** `bundle skill` silently ignores `--temperature`: `parse_options` has no case for it, and the value lands in `remaining`. The repo's CLAUDE.md says it is parsed. The user docs correctly leave it out of `bundle skill`.
- **F-5 (needs Dewayne's decision):** CHANGELOG has no 0.2.1 or 0.2.2 headings, though tags v0.2.1 and v0.2.2 exist and 0.2.2 is on RubyGems. `[Unreleased]` holds what shipped in 0.2.1. The 0.2.2 changes (ruby_llm 2.0, lms/apfel providers, request timeout 300 to 900 s, Ruby floor to 3.4) are not recorded. Release headings belong to Dewayne.
- **F-6 (needs Dewayne's decision):** `GEMSKIL_MAX_TOKENS` is spelled with one L, unlike every other `GEMSKILL_*` variable. Options: (a) also read `GEMSKILL_MAX_TOKENS`, keep the old spelling as a fallback, and document both; (b) leave it as is.
- **F-7 (open, verified):** `gem install --with-skill` (`lib/rubygems_plugin.rb` `generate_one_skill`) calls `Generator#generate` without a block, so it doesn't stream. That is the path `Runner` comments say trips `Net::ReadTimeout` on slow local models.
- **F-8 (unverified):** `configure_llm!` always sets `config.request_timeout`, which overrides a host app's RubyLLM timeout, although its comment says it is a no-op when RubyLLM is already configured.
- **F-9 (needs Dewayne's decision):** `parse_model` splits off any registered provider slug, so OpenRouter-style ids like `openai/gpt-oss-20b` go to the direct provider, not OpenRouter. Options: document `openrouter/openai/...`, or limit splitting to an allowlist.
- **F-10 (open, verified, small):** `test_cache_root_is_in_home_dir` fails when `GEMSKILL_DIR` is set in the environment. The other 173 tests pass either way.
- **F-11 (open):** No test CI workflow. Only the docs-site Pages deploy runs. Adding one is a candidate, possibly needs Dewayne for runner choice.

### 2026-11-07 (F-1 fix pass, develop at 39a14c8; no audit, the backlog was workable)

- **F-1 update:** fixed in [#2](https://github.com/MadBomber/gem-skill/pull/2). The fix uses the model `RubyLLM.chat` already resolved (`chat.model`) instead of a second strict `RubyLLM.models.find` lookup; for local providers RubyLLM falls back to a default model with no `:temperature` metadata. Seen running against a local LM Studio model not in the registry.
- **F-2 decision (taken by rule, recorded here):** don't open a second PR this run. After #2 merges, rebase [bomber/document-local-models](https://github.com/MadBomber/gem-skill/tree/bomber/document-local-models) onto `develop`, add the two missing Apfel notes from F-2 (4,096-token window versus the 32767 default; Apple Intelligence must be enabled), re-run `/simplify` and `/code-review`, and open it as the next gem-skill PR. Rebasing is better than starting over: the branch's `test/docs_test.rb` env-var guard and the Ruby floor fix are still valid.
- **F-12 (PR'd: [#9](https://github.com/MadBomber/gem-skill/pull/9)):** `test/gem/skill/gem_command_test.rb` leaves a `.claude/skills/my_gem` symlink in the current working directory (the repo root) on every `rake test`, pointing into a deleted tmpdir. Some install test links into the default project dir instead of a tmpdir. Fix: stub `Linker`'s project dir (or `GEMSKILL_PROJECT_DIR`) to a tmpdir in that test's setup.
- **F-13 (merged: [#4](https://github.com/MadBomber/gem-skill/pull/4)):** `bundle install` on Ruby 4.0.9 and 4.1.0-preview1 prints `Error loading RubyGems plugin ".../plugins/gem-skill_plugin.rb": uninitialized constant Gem::CommandManager (NameError)` with gem-skill 0.2.2 installed. `lib/rubygems_plugin.rb` line 4 calls `Gem::CommandManager.instance` without requiring `rubygems/command_manager`, which isn't loaded when Bundler loads plugins. Likely fix: `require "rubygems/command_manager"` first; reproduce in a test before fixing.
- **F-14 (needs Dewayne's decision):** `Gem::Skill::Frontmatter.present?` has no callers in lib/, test/, scripts/ or bin/ (debride plus grep). It's a public module method, so removing it needs a deprecation decision. Options: (a) remove it in a minor release; (b) keep it.
- **F-15 (needs Dewayne's decision):** there's no way to turn temperature off for a model whose metadata doesn't say it is unsupported, such as an unregistered local reasoning model. `DEFAULT_TEMPERATURE` is never nil, and the absent-metadata rule sends it. Options: (a) let `GEMSKILL_TEMPERATURE=none` (or `--temperature none`) mean "don't send"; (b) leave it as is until someone reports a rejection.
- **F-10 still open:** recheck on the next run; this run's suite ran with `GEMSKILL_DIR` unset.

### 2026-11-07, second run (F-2 docs pass, develop at 36fcf74; no audit, the backlog was workable)

- **F-2 update:** rebased `bomber/document-local-models` onto develop after #2 merged (clean), added the Apfel notes (Apple Intelligence enabled; lower `GEMSKIL_MAX_TOKENS`; skip `--verify`), and opened [#3](https://github.com/MadBomber/gem-skill/pull/3).
- **F-10 still open, rechecked:** with `GEMSKILL_DIR` set, the suite on develop + #3 gives 180 runs, 1 failure.
- **F-12 still open, rechecked:** every `rake test` still leaves `.claude/` in the repo root.
- **F-16 (merged (low risk): [#5](https://github.com/MadBomber/gem-skill/pull/5), develop 3e384ad):** `mkdocs build --strict` aborts on develop: `docs/commands/gem-skill.md` links `cache.md#metadatajson`, but the cache page is `docs/cache.md`, so the link should be `../cache.md#metadatajson`. The Pages deploy doesn't use `--strict`, so the site still builds, but the link is broken.
- **F-17 (open, verified, small):** `Verifier#build_chat` sets no output-token cap (`with_max_output_tokens`), unlike `Generator`. So `GEMSKIL_MAX_TOKENS` / `--max-tokens` have no effect on `--verify` or `gem skill verify`. Found in #3's review; #3 documents it (skip `--verify` on Apfel) instead of changing code.
- **F-18 (open, verified 2026-11-10):** `ENV_KEY_MAP` doesn't include `OLLAMA_API_BASE`, so an `ollama/...` model is split to the Ollama provider but RubyLLM has no api_base for it. Check whether RubyLLM raises a configuration error, and whether adding the mapping is an obvious-yes PR.
- **F-19 (open, small):** the repo's CLAUDE.md list of key env vars leaves out `GEMSKILL_REQUEST_TIMEOUT` and the `LMS_*`/`APFEL_*` variables.

### 2026-11-07, third run (F-13 fix pass, develop at 5552441; no audit, the backlog was workable)

- **F-13 update:** fixed in [#4](https://github.com/MadBomber/gem-skill/pull/4): `lib/rubygems_plugin.rb` requires `rubygems/command_manager`. A new fresh-process test fails on develop and passes on the branch. A branch build in a temp `GEM_HOME` removes the `bundle install` warning. With the plugin fully loaded under Bundler, its post_install and at_exit hooks run inside `bundle install`; a traced install of a native gem showed them as no-ops (exit 0).
- **F-12 still open, rechecked:** `rake test` still leaves `.claude/` in the repo root.
- **F-16 still open, rechecked:** `docs/commands/gem-skill.md:75` still links `cache.md#metadatajson`.
- **F-20 (open, unverified idea, not obvious-yes):** the plugin eagerly loads all of gem-skill (ruby_llm, async, tty-spinner) just to register `gem skill`. It took about 356 ms in #4's review, and every `gem` and `bundle install` call pays that. RubyGems can load a command lazily from `lib/rubygems/commands/skill_command.rb`, leaving the plugin with only `register_command` plus the install hooks. It's a restructure, so measure it before proposing.
- **F-21 (open, small):** the `test_generate_one_skill_calls_*_on_spinner` tests redefine `success`/`error` on `null_spinner`, which gives "method redefined" warnings under `ruby -w` (rubygems_plugin_test.rb:110 and :120).

### 2026-11-10 security audit + improver pass (develop at 44a608c)

- **S-1:** security finding, details held locally. (medium; obvious-yes PR candidate, top of the next visit)
- **S-2:** security finding, details held locally. (medium; needs Dewayne's decision)
- **S-3:** security finding, details held locally. (low; needs Dewayne's decision)
- **S-4:** security finding, details held locally. (low; needs Dewayne's decision)
- **F-3, F-4 (PR'd: [#10](https://github.com/MadBomber/gem-skill/pull/10)):** still present on develop before #10 (rechecked by triage, auditor and improver).
- **F-7, F-10, F-17, F-19 still open, rechecked.** F-10: `GEMSKILL_DIR=/tmp/x rake test` gives 181 runs, 1 failure. F-21 is wider than recorded: `ruby -w` also warns of method redefinitions in `generator_test.rb` (81, 159, 184, 199, 272); 14 warnings in total.
- **F-18 verified:** with `OLLAMA_API_BASE` set, an `ollama/...` model raises `RubyLLM::ConfigurationError: Ollama provider is not configured`. That error is not a `RubyLLM::Error`, so it isn't wrapped. Fix: add `ollama_api_base: "OLLAMA_API_BASE"` to `ENV_KEY_MAP`, plus doc rows. A small obvious-yes candidate.
- **F-22 (open, low):** `bundle install` inside a gem-skill checkout prints `Error loading RubyGems plugin ".../lib/rubygems_plugin.rb": cannot load such file -- gem/skill (LoadError)`, because `gem_command.rb:8` does a bare `require "gem/skill"`. A consumer project with the gem installed was clean (triage), so this only affects contributors. Changing the require touches the json activation-order workaround; see F-23.
- **F-23 (unverified):** the comments at `gem_command.rb:4-7` and `bundle_command.rb:3-4` say ruby_llm pins json < 3, but a fresh lock resolves ruby_llm 2.1.1 with json 3.1.0. The comments, and maybe the require-order workaround, may be stale. Check in the dependencies audit.
- **F-24 (issue [#8](https://github.com/MadBomber/gem-skill/issues/8)):** `Lockfile.parse_direct_names` keeps the `!` on git-sourced deps (`foo!`), so `bundle skill` silently skips every git gem.
- **F-25 (issue [#6](https://github.com/MadBomber/gem-skill/issues/6)):** `Verifier#extract_skill` accepts a reply with no `===END SKILL===`, so a truncated verify reply replaces the cached skill. One-line fix: `return fallback unless stop`. Land it before or with F-17.
- **F-26 (issue [#7](https://github.com/MadBomber/gem-skill/issues/7)):** `gem skill` exits 0 on every error path (unknown subcommand, missing name, per-gem errors), though the docs and CHANGELOG promise 1.
- **F-27 (open, docs):** `docs/cache.md:57-98` and `docs/how-it-works.md` (84-87, 106-122, 158) still describe `source`, `change_count`, `changes` and `Fetcher#source_manifest`, which commit 783e0c5 removed on purpose. Here the docs should follow the code. About 50 doc lines, mostly deletions; keep the `metadata.json` heading (F-16's anchor).
- **F-28 (open, removal):** `Fetcher` (`fetcher.rb:69-107`) builds `{code:, files:, chars:, truncated:}`, but only `:code` is ever read. The methods are private and have no other readers. The alternative is to record `truncated` in the verify metadata (see inbox).
- **F-29 (open, test quality):** several `gem_command_test.rb` install tests never make a real link: `stub_generator` writes nothing to the cache, `Linker.link` raises, and `Runner` swallows the error, so they pass without proving any linking. Also: `test_helper.rb:5` gives a SimpleCov `add_filter` deprecation warning, and there is no repo `.rubocop.yml`, so lint results depend on `~/.rubocop.yml`.
- **Dead code:** 44a608c..3e384ad (#4, #5) added only a require and a docs link. F-28 is the only new removable code (improver, at 44a608c).
