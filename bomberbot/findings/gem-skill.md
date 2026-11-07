# Findings: gem-skill

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | never | |
| dependencies | never | |
| adoption | 2026-11-06 | first run; README/docs vs 0.2.2 API, CHANGELOG, local-model path exercised without network |
| quality | never | |

Dead-code scan last covered commit: (none; not run 2026-11-06, budget went to the adoption audit)

## Findings

### 2026-11-06 adoption audit (develop at 39a14c8, same as main)

- **F-1 (open, verified; best next PR):** `Generator#temperature_supported?` calls `RubyLLM.models.find`, which raises `RubyLLM::ModelNotFoundError` for any `lms/` or `apfel/` model id not in the static registry. So every `apfel/...` model fails (the Apfel gem ships no models.json) and so does every LM Studio model except the five in ruby_llm-providers-lms's models.json, before any request is sent. Reproduced with `Generator#build_chat` for `lms/qwen/qwen3-4b` and `apfel/apple-foundationmodel`. With `temperature: nil` both build fine, and `Verifier#build_chat` is fine, so the lookup is the only blocker. Also, `ModelNotFoundError` is not a `RubyLLM::Error`, so `Generator#generate`'s rescue doesn't wrap it. Fix: rescue in `model_info`/`temperature_supported?` and treat unknown as supported. Add a failing test.
- **F-2 (open; gave up at the review cap, branch [bomber/document-local-models](https://github.com/MadBomber/gem-skill/tree/bomber/document-local-models)):** README and docs say nothing about the 0.2.2 `lms/` and `apfel/` model prefixes, `LMS_API_BASE`, `LMS_API_KEY`, `APFEL_API_BASE`, `APFEL_API_KEY`, or `GEMSKILL_REQUEST_TIMEOUT`. The README says an API key is required. `docs/installation.md` says Ruby >= 3.2.0 but the gemspec says >= 3.4. The branch fixes all of this and adds `test/docs_test.rb`, which fails when an env var read in lib/ is undocumented. Review round 3 found that the documented example hits F-1, so land F-1 first, then rebase the branch. The Apfel text also needs to cover: the 4,096-token window versus the default `GEMSKIL_MAX_TOKENS` 32767 (tell users to lower it), and the "Apple Intelligence enabled" requirement.
- **F-3 (open, verified):** In `BundlerCommand.parse_options`, a flag that takes a value breaks when placed before the subcommand. `bundle skill --model X install` gives "unknown subcommand X", because the value lands in `remaining`, twice when `--model` is set. Same for `--max-tokens N install`. With the subcommand first it works, as every doc example does.
- **F-4 (open, verified):** `bundle skill` silently ignores `--temperature`: `parse_options` has no case for it, and the value lands in `remaining`. The repo's CLAUDE.md says it is parsed. The user docs correctly leave it out of `bundle skill`.
- **F-5 (needs Dewayne's decision):** CHANGELOG has no 0.2.1 or 0.2.2 headings, though tags v0.2.1 and v0.2.2 exist and 0.2.2 is on RubyGems. `[Unreleased]` holds what shipped in 0.2.1. The 0.2.2 changes (ruby_llm 2.0, lms/apfel providers, request timeout 300 to 900 s, Ruby floor to 3.4) are not recorded. Release headings belong to Dewayne.
- **F-6 (needs Dewayne's decision):** `GEMSKIL_MAX_TOKENS` is spelled with one L, unlike every other `GEMSKILL_*` variable. Options: (a) also read `GEMSKILL_MAX_TOKENS`, keep the old spelling as a fallback, and document both; (b) leave it as is.
- **F-7 (open, verified):** `gem install --with-skill` (`lib/rubygems_plugin.rb` `generate_one_skill`) calls `Generator#generate` without a block, so it doesn't stream. That is the path `Runner` comments say trips `Net::ReadTimeout` on slow local models.
- **F-8 (unverified):** `configure_llm!` always sets `config.request_timeout`, which overrides a host app's RubyLLM timeout, although its comment says it is a no-op when RubyLLM is already configured.
- **F-9 (needs Dewayne's decision):** `parse_model` splits off any registered provider slug, so OpenRouter-style ids like `openai/gpt-oss-20b` go to the direct provider, not OpenRouter. Options: document `openrouter/openai/...`, or limit splitting to an allowlist.
- **F-10 (open, verified, small):** `test_cache_root_is_in_home_dir` fails when `GEMSKILL_DIR` is set in the environment. The other 173 tests pass either way.
- **F-11 (open):** No test CI workflow. Only the docs-site Pages deploy runs. Adding one is a candidate, possibly needs Dewayne for runner choice.
