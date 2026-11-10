# ragdoll

RAG library; most-starred repo, idle since early 2026.

## Links

- GitHub: https://github.com/MadBomber/ragdoll
- RubyGems: https://rubygems.org/gems/ragdoll
- Issues: https://github.com/MadBomber/ragdoll/issues

## Status

_Updated 2026-11-10 00:15 visit._ Dewayne merged #10, #14, #15 and #16 on 2026-11-09. This visit merged [#18](https://github.com/MadBomber/ragdoll/pull/18) as low risk (first #12 slice) and opened [#19](https://github.com/MadBomber/ragdoll/pull/19) (drop `annotate`, fixes #17; develop already locks Rails 8.1.4.1) and [#20](https://github.com/MadBomber/ragdoll/pull/20) (F-19, vision model options), both **PR open**, CI green. Filed issues #21 (F-30), #22 (F-28), #23 (F-20). Next visit: the S-2 + F-29 audio fix first.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- ~~**Get the `develop` test suite green.**~~ Done. Added 2026-11-05. **Merged 2026-11-06:** [#5](https://github.com/MadBomber/ragdoll/pull/5) and [#4](https://github.com/MadBomber/ragdoll/pull/4); together they make CI mode 1293 runs, 0 failures, 0 errors.
  - **Evidence:** CI run https://github.com/MadBomber/ragdoll/actions/runs/42141192445 fails with 3 failures and 322 errors. `main` is green.
  - **Cause (corrected 2026-11-05):** the 322 errors are database tests that lost their CI-mode skip guard in the January test rewrite (`ActiveRecord::ConnectionNotDefined`); the 3 failures are one more unguarded DB test plus two code defects (`Database.connected?` returning nil, batch-embedding fallback ignoring its filter). The `ruby_llm_config` messages are rescued and cause no test errors; that bug is real and tracked as finding F-1.
  - **Original cause note:** the January `anyway_config` migration (6783eb6) left `ImageDescriptionService` and `ImageToTextService` calling `ragdoll_config.ruby_llm_config`, which `Ragdoll::Core::Config` no longer defines. See the FIXME at `app/services/ragdoll/image_description_service.rb:147`. Merging `main` into `develop` (6e80890) added 2 more errors on top of the 320 already present at 0ebbbaf.
  - **Reproduce:** `RAGDOLL_SKIP_DATABASE_TESTS=true bundle exec rake test` on `develop`, under Ruby 4.0.9. Ruby 4.1.0-preview1 crashes Bundler on the gemspec with `uninitialized constant Pathname::SEPARATOR_PAT`.
  - **Size:** likely more than one PR. Start with the `ruby_llm_config` callers, the biggest single cause, and take the merge-introduced errors as their own small PR if they are separate.

## Open bomberbot PRs

- [#19](https://github.com/MadBomber/ragdoll/pull/19) chore(deps): drop annotate, which has no Rails 8 release (fixes #17). **PR open**, CI green on 3.2/3.3/3.4/4.0. Touches the gemspec, so not low risk.
- [#20](https://github.com/MadBomber/ragdoll/pull/20) fix: build ImageToTextService vision model (F-19). **PR open**, CI green. Review hit the cap. Conflicts with #19 in CHANGELOG `[Unreleased]` once one merges (trivial rebase).

## Merged bomberbot PRs

- [#18](https://github.com/MadBomber/ragdoll/pull/18) test: run the search cleanup tests (first slice of #12, F-16). **Merged (low risk)** by bomberbot 2026-11-10 (develop 1ba1414).
- [#16](https://github.com/MadBomber/ragdoll/pull/16) refactor: use bound parameters for search filter conditions. Merged 2026-11-09 (b0a7afc). Not recorded by the visit that opened it.
- [#15](https://github.com/MadBomber/ragdoll/pull/15) docs: fix README Quick Start config to match current API (issue #11, F-18). Merged 2026-11-09 (ca8deeb).
- [#14](https://github.com/MadBomber/ragdoll/pull/14) fix: require activerecord >= 7.1 in gemspec (issue #13, F-9). Merged 2026-11-09 (5670fa9).
- [#10](https://github.com/MadBomber/ragdoll/pull/10) fix: pass converter options to media services (F-17). Merged 2026-11-08 (20f93f7).
- [#9](https://github.com/MadBomber/ragdoll/pull/9) test: report database-only client tests as skips. Merged 2026-11-07.
- [#8](https://github.com/MadBomber/ragdoll/pull/8) ci: add Ruby 4.0 to the test matrix. Merged 2026-11-07.
- [#7](https://github.com/MadBomber/ragdoll/pull/7) chore(deps): update locked gems within current majors. Merged 2026-11-07.
- [#6](https://github.com/MadBomber/ragdoll/pull/6) fix: Database.disconnect! works on ActiveRecord 7.2+. Merged 2026-11-07.
- [#5](https://github.com/MadBomber/ragdoll/pull/5) test: skip database tests when database tests are off. Merged 2026-11-06.
- [#4](https://github.com/MadBomber/ragdoll/pull/4) fix: connected? returns boolean; batch fallback honors filter. Merged 2026-11-06.
