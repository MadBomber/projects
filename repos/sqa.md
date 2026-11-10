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

_First visit (2026-11-10)._ Opened #44 and #46; merged #45 as low risk, so the suite is now green on develop without an FMP key; filed #47-#49. `develop` (7fa65e3) is 13 commits ahead of `main`; `VERSION`
says 0.4.0 but CHANGELOG/.semver/RubyGems (0.0.38) disagree (F-14). No `Gemfile.lock`
committed and no test CI. The suite runs on Ruby 4.0.9 (polars-df has no prebuilt gem for
4.1.0-preview1): 612 runs, red only in `test/data_frame/fmp_test.rb` without `FMP_API_KEY`.
Answered issue #4 (API keys in config) with where develop stands; it needs Dewayne's design call.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- F-16 is now unblocked: F-7 merged as [#45](https://github.com/MadBomber/sqa/pull/45).
- F-9 README examples: **PR open** [#46](https://github.com/MadBomber/sqa/pull/46). Follow-up: docs/ still prints backtest fractions as percent (docs/index.md:84,86; docs/advanced/backtesting.md:27-30,52,64,98-102; docs/strategy.md:273; docs/strategies/bollinger-bands.md:77-80).
- F-16 test CI workflow (TA-Lib .deb, Ruby 3.4 + 4.0), once F-7 merges.
- F-4 dead Ruby 1.9 branch in `String#to_constant`: issue [#47](https://github.com/MadBomber/sqa/issues/47).
- F-10 CHANGELOG `[Unreleased]` backfill: issue [#49](https://github.com/MadBomber/sqa/issues/49) (rebase on #46, which adds one Fixed line).
- F-12 `on_signal` test asserts nothing (+ clears all RuboCop offenses): issue [#48](https://github.com/MadBomber/sqa/issues/48). F-13 unused `$data` fixture in test_helper.
- F-8 raise `required_ruby_version` to `>= 3.4` (sqa-tai needs it); gemspec, so Dewayne reviews.
- S-2 small hardening fix, details held locally.

## Open bomberbot PRs

- [#46](https://github.com/MadBomber/sqa/pull/46) `bomberbot/readme-examples` → develop. docs(readme): examples match the current API (Portfolio, backtest %, strategy names, config data_dir). README + CHANGELOG, +25/-17, offline probe proves old fails/new works. One review round, 5 fixed, 3 out-of-scope findings left open, so not low risk.
- [#44](https://github.com/MadBomber/sqa/pull/44) `bomberbot/drop-root-csv` → develop. chore: remove stray root files (two unreferenced root files; spec.files 256→254). Not low risk (S-1 fix); left for Dewayne.

## Merged bomberbot PRs

- [#45](https://github.com/MadBomber/sqa/pull/45) test(fmp): placeholder API key in stubbed DataFrame tests. **merged (low risk)** 2026-11-10 by bomberbot as develop 3209f5f; full suite 612 runs 0F/0E without `FMP_API_KEY` (Ruby 4.0.9).
