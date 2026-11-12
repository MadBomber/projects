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

_Fifth visit (2026-11-12 05:45)._ Queue still full (#53, #54, #58 open, none approved), so no new PRs. First full dead-code audit (F-41..F-48) plus improver F-49/F-50. Filed #62 (Stream window < 30 silent), #63 (mp_test dead rescue), #64 (orphan examples/F.csv).

_Previous: fourth visit (2026-11-12 03:15)._ Queue still full (#53, #54, #58 open, none approved), so no new PRs. First adoption audit (F-28..F-33); improver found Stream/KBS stochastic+volume bug and MultiTimeframe short-data crash. Filed #59, #60, #61.

_Previous: third visit (2026-11-12)._ Dewayne rebase-merged #44, #46 and #52 (develop **5ea98c8**), so the queue was empty; opened three backlog PRs: [#53](https://github.com/MadBomber/sqa/pull/53) test CI (F-16, green on 3.4/4.0), [#54](https://github.com/MadBomber/sqa/pull/54) docs/ backtest percentages (F-9 follow-up), [#58](https://github.com/MadBomber/sqa/pull/58) Alpha Vantage/Stooq params hash. None low risk. First dependencies audit (F-19..F-22, bundle-audit clean). Filed #55 (RiskManager integer division), #56 (Portfolio cost-basis truncation), #57 (docs keyword drift). #47-#49 fixed on develop, stay open until develop→main. #4 still awaits a design call.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- F-16 test CI workflow: **PR open** [#53](https://github.com/MadBomber/sqa/pull/53) (Ruby 3.4 + 4.0, TA-Lib 0.8.3 .deb).
- F-9 follow-up, docs/ backtest fractions printed as %: **PR open** [#54](https://github.com/MadBomber/sqa/pull/54).
- S-2: details held locally.
- Alpha Vantage/Stooq queries from a params hash: **PR open** [#58](https://github.com/MadBomber/sqa/pull/58).
- [#55](https://github.com/MadBomber/sqa/issues/55) `RiskManager.max_drawdown`/`kelly_criterion` integer division (lib, ~2 lines + test). Good next-visit PR.
- [#56](https://github.com/MadBomber/sqa/issues/56) `Portfolio` avg cost / P&L integer truncation (lib, ~3 lines + test). Good next-visit PR.
- [#57](https://github.com/MadBomber/sqa/issues/57) docs examples with wrong keywords (`initial_cash:` → `initial_capital:`, risk-management page). Docs deploy, so not low risk; split per page.
- F-19 `bin/sqa-console` needs `irb` as a dev dependency on Ruby 4 (gemspec, one line).
- F-13 unused `$data` fixture in test_helper (test-only removal, low risk).
- `SQA.init` calls `CLI.run!` if `SQA::CLI` is defined; sqa-cli defines `SQA::CLI` without `run!`, so loading both and calling `SQA.init` raises (improver, 2026-11-12). Fold into F-15's decision.
- F-8 raise `required_ruby_version` to `>= 3.4` (every native dep needs ≥3.3, sqa-tai ≥3.4); gemspec, so Dewayne reviews. F-6 `changelog_uri` could fold into the same gemspec PR. F-32 (`documentation_uri`, `bug_tracker_uri`) folds in too.
- [#59](https://github.com/MadBomber/sqa/issues/59) Stream vectors give KBS no stochastic/volume facts (F-34; lib ~3 lines + 2 tests). Good next PR.
- [#60](https://github.com/MadBomber/sqa/issues/60) MultiTimeframe raises with < ~2 years of data (F-35; lib ~10 lines + test).
- [#61](https://github.com/MadBomber/sqa/issues/61) README KBS rule + indicator examples fail (F-28/F-29; README-only, low-risk candidate; F-30 SQLite README note could ride along).
- [#62](https://github.com/MadBomber/sqa/issues/62) Stream never emits signals with `window_size` < 30 (F-49; lib ~5 lines + 2 tests; raise ArgumentError). F-50 `puts`→`warn` in the same file could be its own 1-line PR.
- [#63](https://github.com/MadBomber/sqa/issues/63) mp_test dead `rescue NoMethodError; skip` (F-43; test-only, low-risk candidate).
- [#64](https://github.com/MadBomber/sqa/issues/64) orphan 710 KB `examples/F.csv` (F-45; pure deletion; ships in the gem).

## Open bomberbot PRs

- [#58](https://github.com/MadBomber/sqa/pull/58) `madbot/av-query-params` → develop. refactor: build Alpha Vantage queries from a params hash (also Stooq, `Stock#merge_overview`, `Stock.top`). 3 lib + 4 test files + CHANGELOG top line. Fail-before/pass-after tests; suite 622/0F/0E on 4.0.9; rubocop clean; 1 clean review round. lib change → for Dewayne.
- [#54](https://github.com/MadBomber/sqa/pull/54) `madbot/docs-backtest-percent` → develop. docs: show backtest fractions as percentages (10 docs/ files, +24/-24, also fixes ensemble.md `results[:return]`). Offline probe proof; clean review. docs/** deploys Pages, so not low risk.
- [#53](https://github.com/MadBomber/sqa/pull/53) `madbot/test-ci` → develop. ci: run the test suite on GitHub Actions for Ruby 3.4 and 4.0. `.github/workflows/test.yml` + CHANGELOG bottom line; CI green (616/0F/0E both Rubies). `.github/` → not low risk; labeled needs-dewayne.

Trial merge of all three onto develop 5ea98c8 was clean.

## Merged bomberbot PRs

- [#52](https://github.com/MadBomber/sqa/pull/52) `madbot/drop-ruby19-branch` → develop. **merged by Dewayne** by 2026-11-12 (develop 5ea98c8). refactor(patches): drop dead paths in `String#to_constant` (unreachable Ruby 1.9 branch + dead `|| const_missing` arm; `names.reduce(Object, :const_get)`). +test/patches/string_test.rb regression guard. Fixes #47. rubocop clean, suite 616/0F/0E on 4.0.9. Lib change → for Dewayne.
- [#46](https://github.com/MadBomber/sqa/pull/46) `bomberbot/readme-examples` → develop. **merged by Dewayne** by 2026-11-12 (develop 5ea98c8). docs(readme): examples match the current API (Portfolio, backtest %, strategy names, config data_dir). README + CHANGELOG, +25/-17, offline probe proves old fails/new works. One review round, 5 fixed, 3 out-of-scope findings left open, so not low risk. Will need a one-line CHANGELOG rebase now that #51 merged.
- [#44](https://github.com/MadBomber/sqa/pull/44) `bomberbot/drop-root-csv` → develop. **merged by Dewayne** by 2026-11-12 (develop 5ea98c8). chore: remove stray root files (two unreferenced root files; spec.files 256→254). Not low risk (S-1 fix); left for Dewayne.
- [#50](https://github.com/MadBomber/sqa/pull/50) test: assert on_signal callback fires, drop dead gp var. **merged (low risk)** 2026-11-10 as develop 9cee34f (then 795bce1 after #51); Fixes #48 (F-12); clears all 6 RuboCop offenses, repo now lint-clean on `lib test`; suite 612/0F/0E.
- [#51](https://github.com/MadBomber/sqa/pull/51) docs(changelog): record SQLite stores and other develop changes. **merged (low risk)** 2026-11-10 as develop 795bce1; Fixes #49 (F-10); CHANGELOG `[Unreleased]` only, clean 1-round review, CHANGELOG not in the mkdocs site.
- [#45](https://github.com/MadBomber/sqa/pull/45) test(fmp): placeholder API key in stubbed DataFrame tests. **merged (low risk)** 2026-11-10 by bomberbot as develop 3209f5f; full suite 612 runs 0F/0E without `FMP_API_KEY` (Ruby 4.0.9).
