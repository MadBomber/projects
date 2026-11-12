# ragdoll

RAG library; most-starred repo, idle since early 2026.

## Links

- GitHub: https://github.com/MadBomber/ragdoll
- RubyGems: https://rubygems.org/gems/ragdoll
- Issues: https://github.com/MadBomber/ragdoll/issues

## Status

_Updated 2026-11-12 08:30 visit._ **Skipped: review queue full** again: #30, #31, #32 unchanged (open, green, MERGEABLE, unapproved, no comments). Ran the **security** audit (S-5, held locally) plus improver (F-66–F-77); filed #39 (F-66 add_tags), #40 (F-67 enrichment summary/keywords), #41 (F-69 search tracking). Next visit once the queue moves: #39 and F-68 (one-line fixes), then #35, #33.

_Prior 2026-11-12 08:18 visit._ **Skipped: review queue full**: #30, #31, #32 still open, green, MERGEABLE, unapproved, no comments; tender confirmed their CHANGELOG lines merge cleanly pairwise. Ran the overdue **dependencies** audit (F-55–F-60) plus improver (F-61–F-65); filed #36 (F-61 db:console), #37 (F-57 Ruby 4.1 tsort), #38 (F-58 EOL Ruby 3.2 floor). Next visit once the queue moves: #35 (F-48, two-line fix), #33 (F-49); after #30 merges, one gemspec removal PR for F-51 + F-55, then #38.

_Prior 2026-11-12 02:15 visit:_ Dewayne squash-merged all five open PRs (#19, #20, #24, #25, #26) on 2026-11-10/11; queue was empty. Opened three fixes for open issues, all **PR open**, CI green on 3.2/3.3/3.4/4.0, review clean in one round, none low risk: [#32](https://github.com/MadBomber/ragdoll/pull/32) (#27/F-33 tag/proposition extractors), [#31](https://github.com/MadBomber/ragdoll/pull/31) (#28/F-34 search cleanup), [#30](https://github.com/MadBomber/ragdoll/pull/30) (#29/F-39 MIT-LICENSE; needs Dewayne to confirm the copyright line). Ran the first **quality** audit (F-44–F-48); filed #33 (F-49), #34 (F-45), #35 (F-48). The three PRs each add one CHANGELOG `[Unreleased]` line and will conflict after the first merges. Next visit: tend/rebase these three; then F-51 (drop unused searchkick/opensearch) or S-2 + F-29.

_Prior 2026-11-10 06:15 visit:_ opened #24, #25, #26 (issues #23, #21, #22); adoption audit F-35–F-43; filed #27, #28, #29. (Its brain edits were left uncommitted and were committed by the 2026-11-12 visit.)

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- ~~**Get the `develop` test suite green.**~~ Done. Added 2026-11-05. **Merged 2026-11-06:** [#5](https://github.com/MadBomber/ragdoll/pull/5) and [#4](https://github.com/MadBomber/ragdoll/pull/4); together they make CI mode 1293 runs, 0 failures, 0 errors.
  - **Evidence:** CI run https://github.com/MadBomber/ragdoll/actions/runs/42141192445 fails with 3 failures and 322 errors. `main` is green.
  - **Cause (corrected 2026-11-05):** the 322 errors are database tests that lost their CI-mode skip guard in the January test rewrite (`ActiveRecord::ConnectionNotDefined`); the 3 failures are one more unguarded DB test plus two code defects (`Database.connected?` returning nil, batch-embedding fallback ignoring its filter). The `ruby_llm_config` messages are rescued and cause no test errors; that bug is real and tracked as finding F-1.
  - **Original cause note:** the January `anyway_config` migration (6783eb6) left `ImageDescriptionService` and `ImageToTextService` calling `ragdoll_config.ruby_llm_config`, which `Ragdoll::Core::Config` no longer defines. See the FIXME at `app/services/ragdoll/image_description_service.rb:147`. Merging `main` into `develop` (6e80890) added 2 more errors on top of the 320 already present at 0ebbbaf.
  - **Reproduce:** `RAGDOLL_SKIP_DATABASE_TESTS=true bundle exec rake test` on `develop`, under Ruby 4.0.9. Ruby 4.1.0-preview1 crashes Bundler on the gemspec with `uninitialized constant Pathname::SEPARATOR_PAT`.
  - **Size:** likely more than one PR. Start with the `ruby_llm_config` callers, the biggest single cause, and take the merge-introduced errors as their own small PR if they are separate.

## Open bomberbot PRs

- [#32](https://github.com/MadBomber/ragdoll/pull/32) fix: default proposition and tag extractors call RubyLLM chat correctly (issue #27, F-33). **PR open**, CI green, review clean. Touches `app/`, not low risk.
- [#31](https://github.com/MadBomber/ragdoll/pull/31) fix: keep clicked searches in cleanup_old_unused_searches (issue #28, F-34). **PR open**, CI green, review clean, proven on a throwaway pgvector Postgres. Touches `app/`, not low risk.
- [#30](https://github.com/MadBomber/ragdoll/pull/30) fix: ship the MIT-LICENSE file the gemspec declares (issue #29, F-39). **PR open**, CI green, review clean, `needs-dewayne` (confirm copyright line). Gemspec, not low risk.

## Merged bomberbot PRs

- [#25](https://github.com/MadBomber/ragdoll/pull/25) fix: Document#generate_metadata! LoadError (issue #21, F-30). Squash-merged 2026-11-11 (53fc937).
- [#24](https://github.com/MadBomber/ragdoll/pull/24) fix: pass reprocess options to the document converter (issue #23, F-20). Squash-merged 2026-11-11 (ca2c2db).
- [#19](https://github.com/MadBomber/ragdoll/pull/19) chore(deps): drop annotate (issue #17). Squash-merged 2026-11-10 (d675518).
- [#20](https://github.com/MadBomber/ragdoll/pull/20) fix: build ImageToTextService vision model (F-19). Squash-merged 2026-11-10 (c3e175b).
- [#26](https://github.com/MadBomber/ragdoll/pull/26) fix: drop dead file attachment in create_document_from_file (issue #22, F-28). Squash-merged 2026-11-10 (d26f2e2). Its PR body had been overwritten with another repo's text; restored 2026-11-12.
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
