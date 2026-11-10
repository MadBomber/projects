# sqa

"Stock Qualitative Analysis": a Ruby library for technical analysis of stock data,
trading strategies, backtesting and portfolio tracking. Polars-based DataFrames, indicators
via the separate `sqa-tai` gem (TA-Lib), FMP / Yahoo / Alpha Vantage / Stooq price adapters,
SQLite stores (`sqa.db`, `portfolio.db`) on `develop`. Educational, not for real money.
Dependents: sqa-cli, sqa-advisor, sqa-bi, sqa_demo-sinatra.

## Links

- GitHub: https://github.com/MadBomber/sqa
- RubyGems: https://rubygems.org/gems/sqa
- Docs: https://madbomber.github.io/sqa/ (deploys on push to `develop`)
- Issues: https://github.com/MadBomber/sqa/issues

## Status

_Second visit (2026-11-10, resumed after a cutoff)._ Shipped the three open-issue fixes: merged
#50 (F-12/#48 on_signal test) and #51 (F-10/#49 CHANGELOG backfill) as low risk — develop is now
**795bce1** and lint-clean (`rubocop lib test` 0 offenses) — and opened #52 (F-4/#47 dead Ruby 1.9
branch in `String#to_constant`) for Dewayne (lib change). Issues #48/#49 stay open until develop→main
(GitHub only auto-closes `Fixes #N` on the default branch). Quality audit ran (first time): repo is
clean beyond the resolved F-12 — flog/flay gates pass, only new findings F-17 (reek_check Rakefile gate
isn't self-contained, needs Dewayne) and F-18 (detect_double_top/_bottom near-dup, needs Dewayne).
Suite now 616 runs 0F/0E on Ruby 4.0.9 without `FMP_API_KEY`. #44 and #46 from visit 1 still open,
unreviewed. `VERSION` 0.4.0 vs CHANGELOG/.semver/RubyGems (0.0.38) still disagree (F-14). No
`Gemfile.lock` committed, no test CI (F-16). Responder posted nothing new on the open issues (nothing
to add). #4 (API keys) still awaits Dewayne's design call.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- F-16 is now unblocked: F-7 merged as [#45](https://github.com/MadBomber/sqa/pull/45).
- F-9 README examples: **PR open** [#46](https://github.com/MadBomber/sqa/pull/46). Follow-up: docs/ still prints backtest fractions as percent (docs/index.md:84,86; docs/advanced/backtesting.md:27-30,52,64,98-102; docs/strategy.md:273; docs/strategies/bollinger-bands.md:77-80).
- F-16 test CI workflow (TA-Lib .deb, Ruby 3.4 + 4.0), once F-7 merges.
- F-4 dead Ruby 1.9 branch in `String#to_constant`: **PR open** [#52](https://github.com/MadBomber/sqa/pull/52) (Fixes #47, lib change for Dewayne).
- F-10 CHANGELOG `[Unreleased]` backfill: **merged (low risk)** [#51](https://github.com/MadBomber/sqa/pull/51) (Fixes #49). #46 also touches CHANGELOG `[Unreleased]`; it will need a one-line rebase.
- F-12 `on_signal` test asserts nothing (+ clears all RuboCop offenses): **merged (low risk)** [#50](https://github.com/MadBomber/sqa/pull/50) (Fixes #48). F-13 unused `$data` fixture in test_helper still open (test-only removal, candidate next visit).
- F-8 raise `required_ruby_version` to `>= 3.4` (sqa-tai needs it); gemspec, so Dewayne reviews. F-6 `changelog_uri` could fold into the same gemspec PR.
- S-2 small hardening fix, details held locally.

## Open bomberbot PRs

- [#52](https://github.com/MadBomber/sqa/pull/52) `madbot/drop-ruby19-branch` → develop. refactor(patches): drop dead paths in `String#to_constant` (unreachable Ruby 1.9 branch + dead `|| const_missing` arm; `names.reduce(Object, :const_get)`). +test/patches/string_test.rb regression guard. Fixes #47. rubocop clean, suite 616/0F/0E on 4.0.9. Lib change → for Dewayne.
- [#46](https://github.com/MadBomber/sqa/pull/46) `bomberbot/readme-examples` → develop. docs(readme): examples match the current API (Portfolio, backtest %, strategy names, config data_dir). README + CHANGELOG, +25/-17, offline probe proves old fails/new works. One review round, 5 fixed, 3 out-of-scope findings left open, so not low risk. Will need a one-line CHANGELOG rebase now that #51 merged.
- [#44](https://github.com/MadBomber/sqa/pull/44) `bomberbot/drop-root-csv` → develop. chore: remove stray root files (two unreferenced root files; spec.files 256→254). Not low risk (S-1 fix); left for Dewayne.

## Merged bomberbot PRs

- [#50](https://github.com/MadBomber/sqa/pull/50) test: assert on_signal callback fires, drop dead gp var. **merged (low risk)** 2026-11-10 as develop 9cee34f (then 795bce1 after #51); Fixes #48 (F-12); clears all 6 RuboCop offenses, repo now lint-clean on `lib test`; suite 612/0F/0E.
- [#51](https://github.com/MadBomber/sqa/pull/51) docs(changelog): record SQLite stores and other develop changes. **merged (low risk)** 2026-11-10 as develop 795bce1; Fixes #49 (F-10); CHANGELOG `[Unreleased]` only, clean 1-round review, CHANGELOG not in the mkdocs site.
- [#45](https://github.com/MadBomber/sqa/pull/45) test(fmp): placeholder API key in stubbed DataFrame tests. **merged (low risk)** 2026-11-10 by bomberbot as develop 3209f5f; full suite 612 runs 0F/0E without `FMP_API_KEY` (Ruby 4.0.9).
