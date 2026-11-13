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

_Seventh visit (2026-11-13 03:45)._ Dewayne merged #53/#54/#58 (develop 93c16a1) and opened his own #68-#79 covering issues #55-#67, so bomberbot worked the leftover backlog: **merged (low risk)** [#83](https://github.com/MadBomber/sqa/pull/83) (F-13, develop 9fd9a79), opened [#84](https://github.com/MadBomber/sqa/pull/84) (F-59) and [#85](https://github.com/MadBomber/sqa/pull/85) (F-19). Second security audit (S-3, S-4 held locally); filed #80, #81, #82.

_Previous: sixth visit (2026-11-12 08:12)._ Queue still full (#53, #54, #58 open, none approved, develop unchanged at 5ea98c8), so no new PRs. Second quality audit (F-51..F-56) plus improver F-57..F-63. Filed #65 (Yahoo `Stock.new` NameError), #66 (`lazy_update` not coerced), #67 (Sortino denominator).

_Previous: fifth visit (2026-11-12 05:45)._ Queue still full (#53, #54, #58 open, none approved), so no new PRs. First full dead-code audit (F-41..F-48) plus improver F-49/F-50. Filed #62 (Stream window < 30 silent), #63 (mp_test dead rescue), #64 (orphan examples/F.csv).

_Previous: fourth visit (2026-11-12 03:15)._ Queue still full (#53, #54, #58 open, none approved), so no new PRs. First adoption audit (F-28..F-33); improver found Stream/KBS stochastic+volume bug and MultiTimeframe short-data crash. Filed #59, #60, #61.

_Previous: third visit (2026-11-12)._ Dewayne rebase-merged #44, #46 and #52 (develop **5ea98c8**), so the queue was empty; opened three backlog PRs: [#53](https://github.com/MadBomber/sqa/pull/53) test CI (F-16, green on 3.4/4.0), [#54](https://github.com/MadBomber/sqa/pull/54) docs/ backtest percentages (F-9 follow-up), [#58](https://github.com/MadBomber/sqa/pull/58) Alpha Vantage/Stooq params hash. None low risk. First dependencies audit (F-19..F-22, bundle-audit clean). Filed #55 (RiskManager integer division), #56 (Portfolio cost-basis truncation), #57 (docs keyword drift). #47-#49 fixed on develop, stay open until develop→main. #4 still awaits a design call.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- F-16 test CI workflow: merged by Dewayne as [#53](https://github.com/MadBomber/sqa/pull/53) (develop abfae58).
- F-9 follow-up, docs/ backtest %: merged by Dewayne as [#54](https://github.com/MadBomber/sqa/pull/54) (develop 5831af6).
- S-2: details held locally. Fixed on develop by #58 (merged).
- Alpha Vantage/Stooq params hash: merged by Dewayne as [#58](https://github.com/MadBomber/sqa/pull/58) (develop 93c16a1).
- [#55](https://github.com/MadBomber/sqa/issues/55) `RiskManager.max_drawdown`/`kelly_criterion` integer division (lib, ~2 lines + test). Good next-visit PR. **Dewayne's PR [#75](https://github.com/MadBomber/sqa/pull/75) open.**
- [#56](https://github.com/MadBomber/sqa/issues/56) `Portfolio` avg cost / P&L integer truncation (lib, ~3 lines + test). Good next-visit PR. **Dewayne's PR [#76](https://github.com/MadBomber/sqa/pull/76) open.**
- [#57](https://github.com/MadBomber/sqa/issues/57) docs examples with wrong keywords (`initial_cash:` → `initial_capital:`, risk-management page). Docs deploy, so not low risk; split per page. **Dewayne's PR [#79](https://github.com/MadBomber/sqa/pull/79) open.**
- F-19 `bin/sqa-console` needs `irb` on Ruby 4: **PR open** [#85](https://github.com/MadBomber/sqa/pull/85).
- F-13 unused `$data` fixture: **merged (low risk)** [#83](https://github.com/MadBomber/sqa/pull/83) (develop 9fd9a79).
- `SQA.init` calls `CLI.run!` if `SQA::CLI` is defined; sqa-cli defines `SQA::CLI` without `run!`, so loading both and calling `SQA.init` raises (improver, 2026-11-12). Fold into F-15's decision.
- F-8 raise `required_ruby_version` to `>= 3.4` (every native dep needs ≥3.3, sqa-tai ≥3.4); gemspec, so Dewayne reviews. F-6 `changelog_uri` could fold into the same gemspec PR. F-32 (`documentation_uri`, `bug_tracker_uri`) folds in too.
- [#59](https://github.com/MadBomber/sqa/issues/59) Stream vectors give KBS no stochastic/volume facts (F-34; lib ~3 lines + 2 tests). Good next PR. **Dewayne's PR [#74](https://github.com/MadBomber/sqa/pull/74) open.**
- [#60](https://github.com/MadBomber/sqa/issues/60) MultiTimeframe raises with < ~2 years of data (F-35; lib ~10 lines + test). **Dewayne's PR [#77](https://github.com/MadBomber/sqa/pull/77) open.**
- [#61](https://github.com/MadBomber/sqa/issues/61) README KBS rule + indicator examples fail (F-28/F-29; README-only, low-risk candidate; F-30 SQLite README note could ride along). **Dewayne's PR [#78](https://github.com/MadBomber/sqa/pull/78) open.**
- [#62](https://github.com/MadBomber/sqa/issues/62) Stream never emits signals with `window_size` < 30 (F-49; lib ~5 lines + 2 tests; raise ArgumentError). F-50 `puts`→`warn` in the same file could be its own 1-line PR. **Dewayne's PR [#73](https://github.com/MadBomber/sqa/pull/73) open.**
- [#63](https://github.com/MadBomber/sqa/issues/63) mp_test dead `rescue NoMethodError; skip` (F-43; test-only, low-risk candidate). **Dewayne's PR [#72](https://github.com/MadBomber/sqa/pull/72) open.**
- [#65](https://github.com/MadBomber/sqa/issues/65) `Stock.new(source: :yahoo_finance)` raises NameError, no `YahooFinance::TRANSFORMERS` (F-51; lib ~15 lines + test, also dedups the 3 copies).  **Dewayne's PR [#70](https://github.com/MadBomber/sqa/pull/70) open.**
- [#66](https://github.com/MadBomber/sqa/issues/66) `Config` `lazy_update` not coerced, `SQA_LAZY_UPDATE=0` turns it on (F-57; lib 1 line + test). **Dewayne's PR [#69](https://github.com/MadBomber/sqa/pull/69) open.**
- [#67](https://github.com/MadBomber/sqa/issues/67) `RiskManager.sortino_ratio` wrong downside denominator (F-53; 1 line + test; numbers change, say so in CHANGELOG). **Dewayne's PR [#68](https://github.com/MadBomber/sqa/pull/68) open.**
- F-59 PatternMatcher empty clusters: **PR open** [#84](https://github.com/MadBomber/sqa/pull/84). F-55 Backtest gives strategies no volume (lib ~10-15 lines + test; name the field `volumes`, coordinate with #74).
- [#64](https://github.com/MadBomber/sqa/issues/64) orphan 710 KB `examples/F.csv` (F-45; pure deletion; ships in the gem). **Dewayne's PR [#71](https://github.com/MadBomber/sqa/pull/71) open.**
- S-3, S-4: details held locally. S-3 is a small bin/-only PR; recommended next visit.
- [#80](https://github.com/MadBomber/sqa/issues/80) `docs/advanced/fpop.md` treats `fpl_analysis` as a Hash; Quick Start crashes (F-64; docs + 2 comment lines; docs deploy, not low risk).
- [#81](https://github.com/MadBomber/sqa/issues/81) `FPOP.determine_direction` labels (0, +x)/(−x, 0) `:FLAT` (F-65; lib 2 lines + 2 tests; CHANGELOG).
- [#82](https://github.com/MadBomber/sqa/issues/82) `bin/sqa-summary --help` prints old name `sqa-summary.rb` (F-66; 5 lines + test).
- F-67 `Stock.new(source: :typo)` raises NameError, not BadParameterError (lib ~6 lines + test; after #70 merges).
- F-72 `SimpleCov.add_group` deprecated in test_helper (test-only, low-risk candidate).

## Open bomberbot PRs

- [#84](https://github.com/MadBomber/sqa/pull/84) `madbot/pattern-empty-cluster` → develop. fix(pattern_matcher): keep each empty cluster's own centroid (F-59); also sizes clusters by `centroids.size` so short data doesn't crash. +23/-3, fail-before/pass-after, suite 624/0F/0E on 4.0.9, review 2 rounds (round 1 fix), CI green. lib → for Dewayne.
- [#85](https://github.com/MadBomber/sqa/pull/85) `madbot/irb-dev-dep` → develop. build(gemspec): declare irb so sqa-console runs on Ruby 4 (F-19). **Runtime** dependency (sqa-console is in `spec.executables`, ships in 0.0.38), like csv/logger/ostruct. gemspec +1, new test/console_test.rb, CHANGELOG +2. Fail-before/pass-after, suite 623/0F/0E, review clean 1 round, CI green. gemspec → for Dewayne.

Dewayne's own PRs in flight (not bomberbot): #68-#79, one per issue #55-#67 (`fix/issue-NN-*`), all green.

## Merged bomberbot PRs

- [#83](https://github.com/MadBomber/sqa/pull/83) test: drop unused $data fixture from test_helper. **merged (low risk)** 2026-11-13 as develop 9fd9a79 (F-13; -25 test lines, suite 622/0F/0E before and after, clean review, CI green).
- [#58](https://github.com/MadBomber/sqa/pull/58) `madbot/av-query-params` → develop. **merged by Dewayne** 2026-11-12 21:34Z (develop 93c16a1), rebase, no comments.
- [#54](https://github.com/MadBomber/sqa/pull/54) `madbot/docs-backtest-percent` → develop. **merged by Dewayne** 2026-11-12 21:33Z (develop 5831af6).
- [#53](https://github.com/MadBomber/sqa/pull/53) `madbot/test-ci` → develop. **merged by Dewayne** 2026-11-12 21:32Z (develop abfae58). After-merge steps (required checks, F-8) still open.

- [#52](https://github.com/MadBomber/sqa/pull/52) `madbot/drop-ruby19-branch` → develop. **merged by Dewayne** by 2026-11-12 (develop 5ea98c8). refactor(patches): drop dead paths in `String#to_constant` (unreachable Ruby 1.9 branch + dead `|| const_missing` arm; `names.reduce(Object, :const_get)`). +test/patches/string_test.rb regression guard. Fixes #47. rubocop clean, suite 616/0F/0E on 4.0.9. Lib change → for Dewayne.
- [#46](https://github.com/MadBomber/sqa/pull/46) `bomberbot/readme-examples` → develop. **merged by Dewayne** by 2026-11-12 (develop 5ea98c8). docs(readme): examples match the current API (Portfolio, backtest %, strategy names, config data_dir). README + CHANGELOG, +25/-17, offline probe proves old fails/new works. One review round, 5 fixed, 3 out-of-scope findings left open, so not low risk. Will need a one-line CHANGELOG rebase now that #51 merged.
- [#44](https://github.com/MadBomber/sqa/pull/44) `bomberbot/drop-root-csv` → develop. **merged by Dewayne** by 2026-11-12 (develop 5ea98c8). chore: remove stray root files (two unreferenced root files; spec.files 256→254). Not low risk (S-1 fix); left for Dewayne.
- [#50](https://github.com/MadBomber/sqa/pull/50) test: assert on_signal callback fires, drop dead gp var. **merged (low risk)** 2026-11-10 as develop 9cee34f (then 795bce1 after #51); Fixes #48 (F-12); clears all 6 RuboCop offenses, repo now lint-clean on `lib test`; suite 612/0F/0E.
- [#51](https://github.com/MadBomber/sqa/pull/51) docs(changelog): record SQLite stores and other develop changes. **merged (low risk)** 2026-11-10 as develop 795bce1; Fixes #49 (F-10); CHANGELOG `[Unreleased]` only, clean 1-round review, CHANGELOG not in the mkdocs site.
- [#45](https://github.com/MadBomber/sqa/pull/45) test(fmp): placeholder API key in stubbed DataFrame tests. **merged (low risk)** 2026-11-10 by bomberbot as develop 3209f5f; full suite 612 runs 0F/0E without `FMP_API_KEY` (Ruby 4.0.9).
