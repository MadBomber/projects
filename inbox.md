# Inbox

Ideas, questions, and recommendations that need Dewayne's decision. Append with a date; Dewayne prunes.

## 2026-11-05

- Two untracked notes already sit at the repo root (`target_1k_monthly.md`, `starter_rails_app_foundations.md`). Decide whether they live here, in a `notes/` directory, or elsewhere.
- ragdoll: CI starts a pgvector Postgres service but sets `RAGDOLL_SKIP_DATABASE_TESTS=true`, so no database test runs in CI (361 skips once #5 lands). Pointing the suite at that service (create `ragdoll_test`, drop the flag, stop treating `CI=true` as "no database" in `ci_environment?`) would give real DB coverage. Locally every DB test passes against pgvector except finding F-3. Needs your call; it changes what CI enforces.

## 2026-11-06

- aia: no workflow runs the test suite; the only workflow deploys docs. Add a minimal `rake test` workflow on Ruby 4.0.x for PRs to `develop`? Once #157 lands, the suite passes without API keys, so it can run on a stock runner. (findings F-6)
- aia: Dependabot alerts are disabled on MadBomber/aia. Enable them, or rely on bomberbot's bundle-audit pass? (F-7)
- aia: `.rubocop.yml` inherits `../.rubocop-base.yml`, which exists only in `robot_lab_project`, so rubocop fails in a fresh clone or CI. Vendor the base file into the repo, or make the inherit optional? (F-8)
- asgard: is `Asgard::Doctor` public API? `Doctor.duplicate_methods` has been unused in lib since ede783b; only 3 tests call it. Options: delete it and its tests (about 25 lines; recommended), deprecate it first, or keep it. (findings F-3)
- asgard: the suite has one error when `LANG` is unset (F-2), which hits launchd/cron runs such as bomberbot's. A one-line fix in `test_helper`'s `run_asgard` (force captured output to UTF-8) makes an easy next PR.
- debug_me: add a test CI workflow (a matrix of current Rubies) and a `rake test` task; neither exists, so PRs can't show green CI. Also consider raising `required_ruby_version >= 2.3.0` to a supported Ruby. (findings #8)
- debug_me: the gem ships `docs/` (about 2000 lines plus a 127 KB jpg), because `spec.files` comes from `git ls-files`. Limiting it to lib/, README, LICENSE and CHANGELOG would shrink the gem. (findings #9)
- debug_me: the CHANGELOG has no 1.1.3 entry, the 1.1.2 and 1.1.1 dates don't match RubyGems, and `docs/changelog.md` has drifted from `CHANGELOG.md`. Should bomberbot fix the entries, or should the docs page include `CHANGELOG.md`? (findings #7)
- debug_me: `.rultor.yml` and `rubygems.yml.asc` look dead. Remove them? (findings #10)
- debug_me: a wrapper around `debug_me` reports the wrapper's own line. Add a `caller_offset:` option (new API), or document `levels: 1`? (findings #11)
- dev: add a tiny CI workflow that installs asgard and load-checks every `.loki`. After dev#1 there's no `test/`, so the load check is the whole suite. (findings F-2)
- dev: the README Contents table lists neither `branch_report.loki` nor `db_doc.loki` (F-4). A small docs PR for a later run.
- gem-skill: add a minimal test CI workflow (Ruby 3.4 and 4.x, `bundle exec rake test`). Today only the Pages deploy runs, so bomberbot PRs have no checks. (findings F-11)
- gem-skill: `GEMSKIL_MAX_TOKENS` is spelled with one L, unlike every other `GEMSKILL_*` variable. Also read the corrected name and keep the old one as a fallback, or leave it? (F-6)
- gem-skill: provider-prefixed ids split on any registered provider, so `openai/gpt-oss-20b` goes to OpenAI rather than OpenRouter. Document the `openrouter/...` form, or only split on an allowlist (`lms`, `apfel`)? (F-9)
- gem-skill: the CHANGELOG has no 0.2.1 or 0.2.2 sections, and 0.2.2's changes (ruby_llm 2.0, lms/apfel, the 900 s timeout, the Ruby 3.4 floor) aren't recorded. Will you add the headings, or should bomberbot write the entries under a heading you name? (F-5)
- prompt_manager: add a test CI workflow (`test.yml`: setup-ruby 4.0.x, `bundle exec rake test`). `test_helper` already gates 100% coverage "in CI", but no workflow runs the tests, so bomberbot PRs show no checks. Which Ruby versions? (findings F-3)
- prompt_manager: the docs deploy also runs on `develop` pushes (c5e7d62), so the public site shows unreleased docs. Keep it, or deploy from `main` only? (F-4)
- prompt_manager: issue #17 targets the 0.x FileSystemAdapter, which was removed in 1.0. It looks closable as obsolete. (F-5)
- Environment: `bundle install` with bundler 4.0.5 on Ruby 4.1.0-preview1 fails for path/gemspec sources (NameError `Pathname::SEPARATOR_PAT` in `Bundler::Source::Path#generate_bin`). Suites still run from already-installed gems. Upgrade bundler or wait for a fix.
- shared_tools: add a GitHub Actions test workflow (`rake test` on Ruby 3.3/3.4/4.x with `LANG=C.UTF-8`). Today PRs get no checks at all; the only workflow is the docs deploy. (findings F-6)
- shared_tools: browser tools raise `LoadError` (a ScriptError) when no driver gem is present, and RubyLLM's tool loop doesn't rescue it. Switch to a StandardError subclass, or return an `{error:}` result? (findings F-4)
- shared_tools: the gemspec's `if RUBY_VERSION >= "4.0"` bigdecimal pin is evaluated at build time, so 0.7.1 requires bigdecimal >= 4.0 everywhere. Make it unconditional, or move it to the Gemfile? (findings F-8)
- shared_tools: the docs Pages workflow deploys on pushes to `develop` as well as `main`, contrary to the "docs deploy from main" branch model. Intended? (prompt_manager has the same pattern, F-4 there.)
