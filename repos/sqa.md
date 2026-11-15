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

_Eighth visit (2026-11-15 00:47, resumed 02:15)._ Dewayne squash-merged #84/#85 and his own #68-#79, #86-#88 (develop ea405df), clearing every bomberbot issue but #4; bomberbot opened [#92](https://github.com/MadBomber/sqa/pull/92) (F-72), [#93](https://github.com/MadBomber/sqa/pull/93) (F-67), [#94](https://github.com/MadBomber/sqa/pull/94) (S-3), none low risk; third dead-code audit (F-75..F-77); filed #89, #90, #91.

_Previous: seventh visit (2026-11-13 03:45)._ Dewayne merged #53/#54/#58 (develop 93c16a1) and opened his own #68-#79 covering issues #55-#67, so bomberbot worked the leftover backlog: **merged (low risk)** #83 (F-13), opened #84 (F-59) and #85 (F-19). Second security audit (S-3, S-4 held locally); filed #80, #81, #82.

_Previous: sixth visit (2026-11-12 08:12)._ Queue still full (#53, #54, #58 open, none approved, develop unchanged at 5ea98c8), so no new PRs. Second quality audit (F-51..F-56) plus improver F-57..F-63. Filed #65 (Yahoo `Stock.new` NameError), #66 (`lazy_update` not coerced), #67 (Sortino denominator).

_Previous: fifth visit (2026-11-12 05:45)._ Queue still full (#53, #54, #58 open, none approved), so no new PRs. First full dead-code audit (F-41..F-48) plus improver F-49/F-50. Filed #62 (Stream window < 30 silent), #63 (mp_test dead rescue), #64 (orphan examples/F.csv).

_Previous: fourth visit (2026-11-12 03:15)._ Queue still full (#53, #54, #58 open, none approved), so no new PRs. First adoption audit (F-28..F-33); improver found Stream/KBS stochastic+volume bug and MultiTimeframe short-data crash. Filed #59, #60, #61.

_Previous: third visit (2026-11-12)._ Dewayne rebase-merged #44, #46 and #52 (develop **5ea98c8**), so the queue was empty; opened three backlog PRs: [#53](https://github.com/MadBomber/sqa/pull/53) test CI (F-16, green on 3.4/4.0), [#54](https://github.com/MadBomber/sqa/pull/54) docs/ backtest percentages (F-9 follow-up), [#58](https://github.com/MadBomber/sqa/pull/58) Alpha Vantage/Stooq params hash. None low risk. First dependencies audit (F-19..F-22, bundle-audit clean). Filed #55 (RiskManager integer division), #56 (Portfolio cost-basis truncation), #57 (docs keyword drift). #47-#49 fixed on develop, stay open until develop→main. #4 still awaits a design call.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo. (Everything from issues #55-#67 and #80-#82 merged by 2026-11-13; pruned 2026-11-15.)

- F-67 unknown `source:` raises NameError: **PR open** [#93](https://github.com/MadBomber/sqa/pull/93).
- F-72 SimpleCov deprecations: **PR open** [#92](https://github.com/MadBomber/sqa/pull/92) (simplecov `>= 1.0` dev-dep floor left for Dewayne).
- S-3: details held locally. **PR open** [#94](https://github.com/MadBomber/sqa/pull/94). S-4: details held locally; small lib PR, recommended next.
- [#89](https://github.com/MadBomber/sqa/issues/89) Portfolio `total_return`/`profit_loss_percent` and `rebalance` Integer division (F-78/F-79; 3 `fdiv` + tests). Good next PR.
- [#90](https://github.com/MadBomber/sqa/issues/90) CHANGELOG stray line + missing #69-#77 entries, fpop.md `:FLAT` (F-80/F-81; CHANGELOG-only part is a low-risk candidate; rebase after #93 adds its line).
- [#91](https://github.com/MadBomber/sqa/issues/91) Polars `map_elements` `return_dtype` warning per column (F-82; lib ~8 lines + test).
- F-50 `Stream#process_strategies` `puts` → `warn` (1 line + test). F-38 `Strategy#add` bare `BadParameterError` (1 line + test). F-85 #73's bare `ArgumentError` → `BadParameterError`.
- F-60 Stooq/AV raise RuntimeError not ApiError (2 lines + 2 stubbed tests). F-55 Backtest gives strategies no volume (lib ~10-15 lines + test; field `volumes`).
- F-8 `required_ruby_version >= 3.4` + F-6/F-32 gemspec metadata: gemspec, Dewayne reviews.
- `SQA.init` calls `CLI.run!` if `SQA::CLI` is defined; sqa-cli's lacks `run!`. Fold into F-15's decision.

## Open bomberbot PRs

- [#92](https://github.com/MadBomber/sqa/pull/92) `bomberbot/simplecov-group` → develop. test: use SimpleCov group/skip instead of deprecated add_group/add_filter (F-72). test_helper 6 lines; 6 DEPRECATION lines → 0; suite 644/0F/0E; review 2 rounds, 1 deferred (dev-dep floor), so not low risk; `needs-dewayne`; CI green.
- [#93](https://github.com/MadBomber/sqa/pull/93) `bomberbot/stock-unknown-source` → develop. fix(stock): raise BadParameterError for an unknown data source (F-67). Adds `Stock::SOURCES` + `Stock.validate_source!`; +38/-3 (lib, test, CHANGELOG, CLAUDE.md); fail-before/pass-after; suite 646/0F/0E; review cap 3 rounds, round 3 no defects; CI green. lib → for Dewayne.
- [#94](https://github.com/MadBomber/sqa/pull/94) `bomberbot/yf-fetch-shellwords` → develop. fix(sqa-yf-fetch): quote exported session values with Shellwords (S-3); also sends `--print-env` progress to stderr so the documented `eval` works. bin + new test/yf_fetch_test.rb; fail-before/pass-after; suite 646/0F/0E; review cap 3 rounds, ended clean; live run against Yahoo OK; CI green. bin/ + security → for Dewayne.

## Merged bomberbot PRs

- [#85](https://github.com/MadBomber/sqa/pull/85) build(gemspec): declare irb. **merged by Dewayne** 2026-11-13 22:06Z, squash (develop e4e8a6b).
- [#84](https://github.com/MadBomber/sqa/pull/84) fix(pattern_matcher): empty clusters keep own centroid. **merged by Dewayne** 2026-11-13 21:20Z, squash (develop c4188b2).
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
