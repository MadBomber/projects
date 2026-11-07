# Findings: gem-skill

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | 2026-11-07 (quick scan, second run) | lib/ changes 39a14c8..36fcf74 (#2) checked by hand; nothing removable |
| dependencies | never | |
| adoption | 2026-11-06 | first run; README/docs vs 0.2.2 API, CHANGELOG, local-model path exercised without network |
| quality | never | |

Dead-code scan last covered commit: 36fcf74 (2026-11-07 second run, quick scan of the #2 changes)

## Findings

### 2026-11-06 adoption audit (develop at 39a14c8, same as main)

- **F-1 (PR'd: [#2](https://github.com/MadBomber/gem-skill/pull/2)):** `Generator#temperature_supported?` calls `RubyLLM.models.find`, which raises `RubyLLM::ModelNotFoundError` for any `lms/` or `apfel/` model id not in the static registry. So every `apfel/...` model fails (the Apfel gem ships no models.json) and so does every LM Studio model except the five in ruby_llm-providers-lms's models.json, before any request is sent. Reproduced with `Generator#build_chat` for `lms/qwen/qwen3-4b` and `apfel/apple-foundationmodel`. With `temperature: nil` both build fine, and `Verifier#build_chat` is fine, so the lookup is the only blocker. Also, `ModelNotFoundError` is not a `RubyLLM::Error`, so `Generator#generate`'s rescue doesn't wrap it. Fix: rescue in `model_info`/`temperature_supported?` and treat unknown as supported. Add a failing test.
- **F-2 (PR'd: [#3](https://github.com/MadBomber/gem-skill/pull/3); earlier: gave up at the review cap, branch [bomber/document-local-models](https://github.com/MadBomber/gem-skill/tree/bomber/document-local-models)):** README and docs say nothing about the 0.2.2 `lms/` and `apfel/` model prefixes, `LMS_API_BASE`, `LMS_API_KEY`, `APFEL_API_BASE`, `APFEL_API_KEY`, or `GEMSKILL_REQUEST_TIMEOUT`. The README says an API key is required. `docs/installation.md` says Ruby >= 3.2.0 but the gemspec says >= 3.4. The branch fixes all of this and adds `test/docs_test.rb`, which fails when an env var read in lib/ is undocumented. Review round 3 found that the documented example hits F-1, so land F-1 first, then rebase the branch. The Apfel text also needs to cover: the 4,096-token window versus the default `GEMSKIL_MAX_TOKENS` 32767 (tell users to lower it), and the "Apple Intelligence enabled" requirement.
- **F-3 (open, verified):** In `BundlerCommand.parse_options`, a flag that takes a value breaks when placed before the subcommand. `bundle skill --model X install` gives "unknown subcommand X", because the value lands in `remaining`, twice when `--model` is set. Same for `--max-tokens N install`. With the subcommand first it works, as every doc example does.
- **F-4 (open, verified):** `bundle skill` silently ignores `--temperature`: `parse_options` has no case for it, and the value lands in `remaining`. The repo's CLAUDE.md says it is parsed. The user docs correctly leave it out of `bundle skill`.
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
- **F-12 (open, verified, small):** `test/gem/skill/gem_command_test.rb` leaves a `.claude/skills/my_gem` symlink in the current working directory (the repo root) on every `rake test`, pointing into a deleted tmpdir. Some install test links into the default project dir instead of a tmpdir. Fix: stub `Linker`'s project dir (or `GEMSKILL_PROJECT_DIR`) to a tmpdir in that test's setup.
- **F-13 (open, verified):** `bundle install` on Ruby 4.0.9 and 4.1.0-preview1 prints `Error loading RubyGems plugin ".../plugins/gem-skill_plugin.rb": uninitialized constant Gem::CommandManager (NameError)` with gem-skill 0.2.2 installed. `lib/rubygems_plugin.rb` line 4 calls `Gem::CommandManager.instance` without requiring `rubygems/command_manager`, which isn't loaded when Bundler loads plugins. Likely fix: `require "rubygems/command_manager"` first; reproduce in a test before fixing.
- **F-14 (needs Dewayne's decision):** `Gem::Skill::Frontmatter.present?` has no callers in lib/, test/, scripts/ or bin/ (debride plus grep). It's a public module method, so removing it needs a deprecation decision. Options: (a) remove it in a minor release; (b) keep it.
- **F-15 (needs Dewayne's decision):** there's no way to turn temperature off for a model whose metadata doesn't say it is unsupported, such as an unregistered local reasoning model. `DEFAULT_TEMPERATURE` is never nil, and the absent-metadata rule sends it. Options: (a) let `GEMSKILL_TEMPERATURE=none` (or `--temperature none`) mean "don't send"; (b) leave it as is until someone reports a rejection.
- **F-10 still open:** recheck on the next run; this run's suite ran with `GEMSKILL_DIR` unset.

### 2026-11-07, second run (F-2 docs pass, develop at 36fcf74; no audit, the backlog was workable)

- **F-2 update:** rebased `bomber/document-local-models` onto develop after #2 merged (clean), added the Apfel notes (Apple Intelligence enabled; lower `GEMSKIL_MAX_TOKENS`; skip `--verify`), and opened [#3](https://github.com/MadBomber/gem-skill/pull/3).
- **F-10 still open, rechecked:** with `GEMSKILL_DIR` set, the suite on develop + #3 gives 180 runs, 1 failure.
- **F-12 still open, rechecked:** every `rake test` still leaves `.claude/` in the repo root.
- **F-16 (open, verified, small):** `mkdocs build --strict` aborts on develop: `docs/commands/gem-skill.md` links `cache.md#metadatajson`, but the cache page is `docs/cache.md`, so the link should be `../cache.md#metadatajson`. The Pages deploy doesn't use `--strict`, so the site still builds, but the link is broken.
- **F-17 (open, verified, small):** `Verifier#build_chat` sets no output-token cap (`with_max_output_tokens`), unlike `Generator`. So `GEMSKIL_MAX_TOKENS` / `--max-tokens` have no effect on `--verify` or `gem skill verify`. Found in #3's review; #3 documents it (skip `--verify` on Apfel) instead of changing code.
- **F-18 (unverified):** `ENV_KEY_MAP` doesn't include `OLLAMA_API_BASE`, so an `ollama/...` model is split to the Ollama provider but RubyLLM has no api_base for it. Check whether RubyLLM raises a configuration error, and whether adding the mapping is an obvious-yes PR.
- **F-19 (open, small):** the repo's CLAUDE.md list of key env vars leaves out `GEMSKILL_REQUEST_TIMEOUT` and the `LMS_*`/`APFEL_*` variables.
