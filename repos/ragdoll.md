# ragdoll

RAG library; most-starred repo, idle since early 2026.

## Links

- GitHub: https://github.com/MadBomber/ragdoll
- RubyGems: https://rubygems.org/gems/ragdoll
- Issues: https://github.com/MadBomber/ragdoll/issues

## Status

_Updated 2026-11-08 09:22 visit._ #9 merged; this visit opened [#10](https://github.com/MadBomber/ragdoll/pull/10), which makes image and audio files reach their converters instead of being stored as filename placeholders (finding F-17), **PR open**, CI green on 3.2/3.3/3.4/4.0. Filed issues #11 (README Quick Start config fails), #12 (43 private search tests, F-16) and #13 (activerecord floor, F-9).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- ~~**Get the `develop` test suite green.**~~ Done. Added 2026-11-05. **Merged 2026-11-06:** [#5](https://github.com/MadBomber/ragdoll/pull/5) and [#4](https://github.com/MadBomber/ragdoll/pull/4); together they make CI mode 1293 runs, 0 failures, 0 errors.
  - **Evidence:** CI run https://github.com/MadBomber/ragdoll/actions/runs/42141192445 fails with 3 failures and 322 errors. `main` is green.
  - **Cause (corrected 2026-11-05):** the 322 errors are database tests that lost their CI-mode skip guard in the January test rewrite (`ActiveRecord::ConnectionNotDefined`); the 3 failures are one more unguarded DB test plus two code defects (`Database.connected?` returning nil, batch-embedding fallback ignoring its filter). The `ruby_llm_config` messages are rescued and cause no test errors; that bug is real and tracked as finding F-1.
  - **Original cause note:** the January `anyway_config` migration (6783eb6) left `ImageDescriptionService` and `ImageToTextService` calling `ragdoll_config.ruby_llm_config`, which `Ragdoll::Core::Config` no longer defines. See the FIXME at `app/services/ragdoll/image_description_service.rb:147`. Merging `main` into `develop` (6e80890) added 2 more errors on top of the 320 already present at 0ebbbaf.
  - **Reproduce:** `RAGDOLL_SKIP_DATABASE_TESTS=true bundle exec rake test` on `develop`, under Ruby 4.0.9. Ruby 4.1.0-preview1 crashes Bundler on the gemspec with `uninitialized constant Pathname::SEPARATOR_PAT`.
  - **Size:** likely more than one PR. Start with the `ruby_llm_config` callers, the biggest single cause, and take the merge-introduced errors as their own small PR if they are separate.

## Open bomberbot PRs

- [#10](https://github.com/MadBomber/ragdoll/pull/10) fix: pass converter options to media services (finding F-17). **PR open**, CI green.

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/ragdoll/pull/9) test: report database-only client tests as skips. Merged 2026-11-07.
- [#8](https://github.com/MadBomber/ragdoll/pull/8) ci: add Ruby 4.0 to the test matrix. Merged 2026-11-07.
- [#7](https://github.com/MadBomber/ragdoll/pull/7) chore(deps): update locked gems within current majors. Merged 2026-11-07.
- [#6](https://github.com/MadBomber/ragdoll/pull/6) fix: Database.disconnect! works on ActiveRecord 7.2+. Merged 2026-11-07.
- [#5](https://github.com/MadBomber/ragdoll/pull/5) test: skip database tests when database tests are off. Merged 2026-11-06.
- [#4](https://github.com/MadBomber/ragdoll/pull/4) fix: connected? returns boolean; batch fallback honors filter. Merged 2026-11-06.
