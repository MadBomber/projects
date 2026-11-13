# Findings: sqa

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-13 | Second run, at develop 93c16a1. bundle-audit clean (lockfile resolved in scratch). #58 params hash verified to encode query values; SQL binds, YAML safe load, lib curl shell-out escaped; bin/ scripts now read too. New: S-3, S-4 (held locally). |
| dead code | 2026-11-12 | Full run at develop 5ea98c8: debride 1.15.2 over lib+bin+test+examples, every candidate grepped by hand, dynamic-dispatch sites checked, dependents sqa-cli/sqa-advisor/sqa-bi/sqa_demo-sinatra cloned to scratch. No unused private methods, error classes or orphan docs pages. New: F-41..F-48. |
| dependencies | 2026-11-12 | First run at develop 5ea98c8 (Ruby 4.0.9, resolved in scratch). bundle outdated: only indirect gems behind (prism via flog, unicode-display_width via tty-table/strings, redis-client via redis = F-1); no gemspec limit blocks a newer release. bundle-audit clean. Dep Ruby floors confirm F-8 (sqa-tai ≥3.4; polars-df/eps/shared_tools ≥3.3; native gems <4.1.dev). New: F-19..F-22. |
| adoption | 2026-11-12 | First run at develop 5ea98c8 (Ruby 4.0.9, offline seeded store). README blocks run: 3 broken (F-28 KBS syntax, F-29 indicator snippet, F-9 holding_period still). Examples 01-04/06-08 exit 0 offline (05/09 need network). `mkdocs build --strict` exit 0; api-reference stale (F-31). llms.txt 50/50 links resolve. gem-skill not run. New: F-28..F-33. |
| quality | 2026-11-12 | Second run at develop 5ea98c8 (Ruby 4.0.9, scratch bundle). rubocop lib/test/bin/examples 0 offenses; flog and flay gates pass (flog top still 46.6, F-18; flay -m 30 groups reviewed by hand); reek with a scratch config: no unused params or private methods (F-17 still blocks reek_check); suite 616 runs 0F/0E/87S, 64.6% line coverage. New: F-51..F-56. |

Dead-code scan last covered commit: 5ea98c8 (develop, 2026-11-12; lib + bin + test + examples, dependents sqa-cli 4406392, sqa-advisor 1bd6b08, sqa-bi b020cbb, sqa_demo-sinatra d800f76).

## Findings

### 2026-11-10 (first visit; security audit + brief dead-code pass + improver, develop 7fa65e3)

- S-1: security finding, details held locally. Status: merged [#44](https://github.com/MadBomber/sqa/pull/44) by Dewayne + needs Dewayne's decision on the rest.
- S-2: security finding, details held locally. Status: PR'd (2026-11-12; PR reference held locally).
- F-1: five runtime deps never loaded (`redis`, `ruby_llm`, `shared_tools`, `eps`, `regent`; `sqa.gemspec:51-60`). `redis` comes in for kbs, which `require`s it eagerly but lists it only as a dev dep. Status: needs Dewayne's decision (gemspec; downstream may rely on them transitively).
- F-2: `SQA::PluginManager` (`lib/sqa/plugin_manager.rb`) is never required; `Config#inject_additional_properties` has no caller. Status: needs Dewayne's decision (wire up vs remove; public API).
- F-3: top-level `AlphaVantageAPI` (`lib/api/alpha_vantage_api.rb`, 458 lines) is loaded by `require 'sqa'` but unused anywhere in sqa or the local dependents. Status: needs Dewayne's decision (deprecate/remove/keep).
- F-4: unreachable Ruby 1.9 branch and dead `|| const_missing` in `String#to_constant` (`lib/patches/string.rb:42-46`). Status: merged [#52](https://github.com/MadBomber/sqa/pull/52) by Dewayne (develop 5ea98c8).
- F-5: gem ships repo clutter (`spec.files` = git ls-files minus a few dirs: 254 files, ~7 MB; docs/, examples/rails_app, checksums/, notes, `trace.log`, `main.just`). Status: needs Dewayne's decision (gemspec allowlist is never low risk).
- F-6: `changelog_uri` = homepage (`sqa.gemspec:26`). Status: open (gemspec; fold into a gemspec PR).
- F-7: suite red on develop without `FMP_API_KEY`: `test/data_frame/fmp_test.rb` 3F/4E because the key is read before the stubbed request. Status: merged (low risk) [#45](https://github.com/MadBomber/sqa/pull/45), develop 3209f5f.
- F-8: `required_ruby_version >= 3.2` but sqa-tai `~> 0.4` needs `>= 3.4`; docs say Ruby 3.2+/3.3.6. Status: open (gemspec + docs; follows debug_me#23 precedent).
- F-9: README examples don't match the API (Portfolio `load_from_csv`/`save_trades_to_csv`/summary keys, backtest fractions printed as %, nonexistent `MomentumBreakout`/`MeanReversion`, `Config.new(data_dir:)`). Status: merged [#46](https://github.com/MadBomber/sqa/pull/46); docs/ follow-up PR open [#54](https://github.com/MadBomber/sqa/pull/54); remaining docs keyword drift is issue [#57](https://github.com/MadBomber/sqa/issues/57). `holding_period:` (README:411) and `gem install sqa-cli` (README:102) need Dewayne's decision.
- F-10: CHANGELOG `[Unreleased]` misses SQLite stores (b185b83), Yahoo window (7838c44), new bin scripts and rename, dependency changes (7411d07), nil guards (89e02af). Status: merged (low risk) [#51](https://github.com/MadBomber/sqa/pull/51), develop 795bce1 (Fixes #49).
- F-11: no docs page mentions `sqa.db`/`portfolio.db`/`SQA::Store`; concepts/file_formats still describe per-ticker CSV/JSON. Status: open (docs deploy from develop, so not low risk).
- F-12: `test_on_signal_callback` (test/stream_test.rb:77-92) asserts nothing; unused var in gp_test.rb. Fixing clears all 6 RuboCop offenses. Status: merged (low risk) [#50](https://github.com/MadBomber/sqa/pull/50), develop 9cee34f (Fixes #48); repo now lint-clean on `lib test`.
- F-13: unused `$data` fixture in `test/test_helper.rb:75-99`. Status: **merged (low risk)** [#83](https://github.com/MadBomber/sqa/pull/83) 2026-11-13, develop 9fd9a79.
- F-14: version numbers disagree: `VERSION = '0.4.0'`, CHANGELOG top release `[0.1.0]` above `[0.3.0]`, `.semver` 0.0.30, RubyGems 0.0.38. Status: needs Dewayne's decision.
- F-15: `SQA.init(argv)` discards its argument though YARD advertises `--debug --verbose` (`lib/sqa/init.rb:35-37`). Status: needs Dewayne's decision (taste rule says fix code; small design choice). 2026-11-12: improver also found `SQA.init` runs `CLI.run! if defined? CLI` (`lib/sqa/init.rb:42-44`); sqa-cli defines `SQA::CLI` with no `run!`, so `SQA.init` raises NoMethodError when both are loaded. Settle together.
- F-16: no test CI workflow; needs TA-Lib C lib, Ruby 3.4/4.0 matrix (polars-df has no 4.1 prebuilt). Status: PR open [#53](https://github.com/MadBomber/sqa/pull/53) (CI green).

### 2026-11-10 (second visit, resumed; quality audit, develop 3209f5f)

- F-17: `rake reek_check` (Rakefile:135-190) is not self-contained in a clean checkout. `git ls-files | grep -iE 'reek|\.quality'` tracks neither `.reek.yml` nor `.quality/reek_baseline.txt`. Two defects: (a) with no `.reek.yml` resolving up-tree the task passes `configuration: nil` to `Reek::Examiner.new`, which raises on reek 6.x (`undefined method 'directive_for' for nil`); (b) it reads an untracked `.quality/reek_baseline.txt`, so an empty baseline marks every smelly lib file NEW and the gate aborts. Works in Dewayne's workspace (shared `.reek.yml` up-tree) and no CI runs it (F-16), so it only bites a fresh clone / contributor / future CI. The Rakefile is shared asgard-style tooling copied across the sqa gems, so a fix should be consistent. Status: needs Dewayne's decision (shared tooling + baseline generation is his call). Overlaps F-16.
- F-18: `PatternMatcher#detect_double_top` / `#detect_double_bottom` (`lib/sqa/pattern_matcher.rb:386-420, 422-453`) are mirror-image near-duplicates (the file's two highest-complexity methods, flog 46.6 each), differing only in peaks↔valleys and min↔max/drop↔rise; `detect_head_shoulders` (455) and `detect_triangle` (486) share the same scaffolding. flay doesn't flag it (below mass 50). A DRY extraction parameterizing direction is a readability judgment call, not a provable defect — flog/flay gates already pass. Status: needs Dewayne's decision.

Dead-code scan last covered commit: 7fa65e3 (develop, 2026-11-10; `lib/` only, brief). Not re-run this visit; quality audit ran instead.

### 2026-11-12 (third visit; dependencies audit + triage + improver, develop 5ea98c8)

- F-19: `bin/sqa-console` (the gem's only executable) raises `cannot load such file -- irb (LoadError)` under `bundle exec` on Ruby 4.0: `irb` is a bundled gem and not in the gemspec/lock. Fix: `spec.add_development_dependency 'irb'`. Status: **PR open** [#85](https://github.com/MadBomber/sqa/pull/85) (runtime dependency, since sqa-console ships as an executable).
- F-20: most runtime deps have no version constraint, including pre-1.0 `polars-df` (locked 0.27.1), `hashie` 6.0, `toml-rb` 6.0, `kbs`, `myway_config`. Status: needs Dewayne's decision (pinning policy).
- F-21: `Pathname` used in `lib/sqa.rb:13`, `init.rb:122`, `strategy.rb:78` with no `require 'pathname'`; fine on 4.x (preloaded), probably NameError on 3.x without bundler. Status: unverified (no 3.x Ruby here; moot if floor moves past 3.x, see F-8).
- F-22: `yard-markdown` dev dependency unused (no `.yardopts`, `bin/generate_api_docs.rb` uses plain yard). Status: open (gemspec removal).
- F-23: `RiskManager.max_drawdown` / `kelly_criterion` integer division gives wrong results for Integer inputs, contradicting the YARD example. Status: issue [#55](https://github.com/MadBomber/sqa/issues/55).
- F-24: `Portfolio` avg cost, partial-sell cost basis and `Position#profit_loss_percent` truncate with Integer prices/shares. Status: issue [#56](https://github.com/MadBomber/sqa/issues/56).
- F-25: docs examples pass keywords `Backtest.new`/`RiskManager` don't accept (`initial_cash:` in 13 snippets, risk-management.md, portfolio-optimizer.md, strategy-generator.md, README `results.equity_curve`, `Strategy.descendants`). Status: issue [#57](https://github.com/MadBomber/sqa/issues/57).
- F-26: top-level `NotImplemented` (`lib/sqa/errors.rb:77-92`) and `HOME` (`lib/sqa.rb:12-14`) constants unused in sqa and sqa-cli; `DataFrame.normalize_keys`/`.aofh_to_hofa`/`.sanitize_key`, `Stock#update_the_dataframe` have no internal callers. Status: needs Dewayne's decision (public API; batch into a 1.0 removal list).
- F-27: `lib/sqa/ticker.rb:50` still interpolates `country` into its query string (internal input only). Status: open (consistency follow-up to #58).

### 2026-11-12 03:15 (fourth visit; adoption audit + improver, develop 5ea98c8)

- F-28: README KBS custom-rule example (`README.md:498-534`) doesn't parse (`then do`); bare `assert` inside `perform` also fails. Working form is `kb = strategy.kb` + `perform { kb.assert(...) }` (docs/strategies/kbs.md, examples/04). Status: issue [#61](https://github.com/MadBomber/sqa/issues/61) (README-only, low-risk PR candidate).
- F-29: README Technical Indicators snippet (`README.md:234-241`) raises: `SQAI.rsi` period 14 on 10 prices; SMA comment shows 104.0, actual 103.4. Minor: README:882 `Pattern#to_s` context placement. Status: issue [#61](https://github.com/MadBomber/sqa/issues/61).
- F-30: README never mentions `sqa.db`/SQLite; "CSV files" section (README:1023-1027) says cached `ticker.csv` is loaded directly, but `Stock#adopt_legacy_csv` imports it once into `sqa.db`. README counterpart of F-11. Status: open (README-only, low-risk PR candidate).
- F-31: generated API reference stale (`docs/api-reference/index.md` "Last updated: 2025-11-24"; no pages for `SQA::Store*`, `DataFrame::Fmp`, `Stooq`, `DailyPriceSource`, `SQA::FMP`). `bin/generate_api_docs.rb` regen: 52 → 64 pages, `mkdocs build --strict` still exit 0. Status: open (docs deploy → not low risk; large generated diff).
- F-32: gemspec metadata lacks `documentation_uri` and `bug_tracker_uri` (`sqa.gemspec:24-27`). Status: open (fold into the F-6/F-8 gemspec PR).
- F-33: `SQA DEPRECATION` warning (`lib/sqa/config.rb:259-267`) fires under `ruby -w` on every `require 'sqa'`, before the app can call `SQA.init`, so following its advice can't silence it. Status: needs Dewayne's decision (settle with F-15).
- `docs/llms.txt` minor: line 9 squashes three bullets; lines 27/59/70/91 use raw headings as descriptions; no link to api-reference or a storage page. Fold into the F-11/F-31 docs PR.
- F-34: `SQA::Stream#build_vector` passes `(highs, lows, prices)` into `calculate_indicator(name, prices, _volumes, highs, lows)`, so `stoch_k`/`stoch_d` are always nil (`lib/sqa/stream.rb:280-281, 300`); and `KBS#assert_volume_facts` reads `vector.volume` while Stream provides `volumes` (`kbs_strategy.rb:198-200`). Together 4 of 12 default KBS rules can't fire from a stream (sqa-cli `stream -s KBS`). Status: issue [#59](https://github.com/MadBomber/sqa/issues/59) (~3 lib lines + 2 tests; good next PR).
- F-35: `MultiTimeframe#detect_divergence`/`confirmation`/`indicators` raise `InvalidParameterError` with < ~2 years of data (monthly series < 14 bars); `signal` slices nil below ~50 days. Tests gated behind `RUN_INTEGRATION_TESTS`. Status: issue [#60](https://github.com/MadBomber/sqa/issues/60) (~10 lib lines + 1 test).
- F-36: `Ensemble` vote ties (majority, weighted, confidence) resolve to `:buy` via `max_by` first-key order; `Stream#consensus_signal` and `Strategy::Consensus` hold on ties. Status: open (behaviour change; small PR but say so in the body).
- F-37: `PatternMatcher#future_outcome` (`pattern_matcher.rb:241-256`) measures N+1 periods (`future_end = future_start + periods` with base `future_start - 1`); `find_similar`'s `future_return` same. Status: open (one line + linear-price test; changes forecast numbers).
- F-38: `Strategy#add` (`strategy.rb:40`) raises a bare `BadParameterError` with no message for a Proc or Symbol. Status: open (one-line message + test).
- F-39: `Strategy::MP` and `Strategy::MR` always `:hold` with any vector the library builds (nothing sets `market_profile`/`mr` outside their tests; checked sqa-cli, sqa-advisor, sqa-bi); `Ensemble#select_strategy` picks MR for every sideways regime. Status: needs Dewayne's decision (compute the keys / deprecate / document caller must supply).
- F-40: `Strategy::Consensus` is five random votes but documented as a strategy. Status: needs Dewayne's decision (label as demo in YARD, or deprecate).

### 2026-11-12 05:45 (fifth visit; full dead-code audit + improver, develop 5ea98c8)

- F-41: `SQA::Strategy#auto_load` (`lib/sqa/strategy.rb:77-96`) is a no-op: it `load`s files `strategy.rb:113-125` already required, never adds to `@strategies`, prints `loading x ... done`, and warns about redefined constants (`s.auto_load(only: [:rsi]); s.strategies.size # => 0`). No callers in sqa or the four dependents; documented in `docs/strategy.md:66-76`, `docs/api/index.md:345-351`. Status: needs Dewayne's decision (public API: remove, or make it `add` what it finds).
- F-42: `lib/sqa.rb:48-51` requires `lite/statistics`, its Enumerable/Array monkey patches, and `tty-table`, but nothing in sqa or the dependents uses them (sqa-cli requires its own tty-table). Suite identical without the three requires (616 runs, 0F/0E, 87 skips). Status: needs Dewayne's decision (removing a global monkey patch is user-visible; gemspec deps `sqa.gemspec:46,56`).
- F-43: `test/strategy/mp_test.rb:7-41` wraps three tests in `rescue NoMethodError; skip "MP strategy has syntax error on line 9"`; the bug is gone (5 runs, 0 skips), so the rescue would hide a real regression. Test-only removal. Status: issue [#63](https://github.com/MadBomber/sqa/issues/63) (low-risk candidate; moot if F-39 removes MP).
- F-44: five tests never run even with `RUN_INTEGRATION_TESTS` because an unconditional `skip "Long running test"` follows the gate: `test/strategy_generator_test.rb:52,68,87,107`, `test/gp_test.rb:98`. Only tests of `discover_patterns`/`generate_strategy` and `evolve` history. Status: open (gate on `RUN_SLOW_TESTS` or delete; needs a run to prove they pass).
- F-45: `examples/F.csv` (710 KB, Ford daily prices) referenced nowhere; `download_prices.rb F` output committed in 3ea1acc; ships in the gem (F-5). Status: issue [#64](https://github.com/MadBomber/sqa/issues/64) (pure deletion).
- F-46: more unreferenced public API for the F-26 1.0 list: `SQA::Store::Market::PRICE_COLUMNS` (`store/market.rb:25-27`), `Stock#strategy`/`#strategy=` (`stock.rb:76-78`, never set or read), String aliases `to_snakecase`/`snakecase`/`snake_case`/`camelcase` (`patches/string.rb:19-21,31`). Status: needs Dewayne's decision (batch with F-26).
- F-47: `Strategy::Common#desc` (`strategy/common.rb:69-80`) `puts` and returns nil, but `lib/sqa/strategy/README.md` says it returns a String; only `random.md` exists. Status: open (behaviour change: return the String; one test).
- F-48: `lib/sqa/strategy/README.md` stale (6 of 12 strategies, "OpenStruct" vector, typos `:bur`). Status: open (fold into F-11/F-31 docs pass).
- F-49 (improver C-1): `Stream` never runs strategies when `window_size` < 30 (`sufficient_data?` needs 30, `trim_buffers` caps at `window_size`), silently; sqa-cli passes `-w` through. Repro: window 20/29 → 0 signals, 30 → 1. Status: issue [#62](https://github.com/MadBomber/sqa/issues/62).
- F-50 (improver C-2): `Stream#process_strategies` (`stream.rb:236`) reports strategy failures with `puts` to stdout; every strategy uses `warn`. One line + `assert_output` test. Status: open (good next small PR; stream.rb, no overlap with #59 lines).

### 2026-11-12 08:12 (sixth visit; quality audit + improver, develop 5ea98c8)

- F-51: `SQA::Stock.new(ticker:, source: :yahoo_finance)` always raises `NameError: uninitialized constant SQA::DataFrame::YahooFinance::TRANSFORMERS` (`stock.rb:115`; `yahoo_finance.rb` defines none; AV/FMP/STOOQ each define an identical copy). It is the documented example (`stock.rb:12,92`, README, `docs/data_frame.md:35`) and `FALLBACK_SOURCE`; no test builds a Yahoo `Stock`. Fix: one shared `TRANSFORMERS` in YahooFinance, others reuse it; test per source. Status: issue [#65](https://github.com/MadBomber/sqa/issues/65) (good next PR, ~15 lines + test).
- F-52: `Stream#lazy_indicator` (`stream.rb:288-295`) isn't lazy: calls the lambda immediately, runs `bbands` 3× and `stoch` 2× per bar, and a bare `rescue` returns nil (it hid F-34). Fix: compute once per bar, narrow the rescue. Status: open (land after #59; same lines).
- F-53: `RiskManager.sortino_ratio` divides downside squares by the count of losing periods, not N (`risk_manager.rb:302-304`); `[0.02,-0.01,0.03,0.01,-0.02,0.015]` gives 0.4743 vs textbook 0.8216; test only checks Float. Status: issue [#67](https://github.com/MadBomber/sqa/issues/67) (1 line + test; numbers change).
- F-54: `PortfolioOptimizer.risk_parity` returns NaN weights when any asset has zero volatility (`portfolio_optimizer.rb:155`, `1.0 / v`). Fix: raise `BadParameterError` or define the weight. Status: open (raising is a small behaviour call; also improver noted `rebalance(total_value: 0)` divides by zero).
- F-55: `Backtest#generate_signal` (`backtest.rb:248-257`) builds vectors from prices + rsi only, so `VolumeBreakout` never trades in a backtest (direct call `:buy`, via Backtest `:hold`); KBS gets rsi only too. Fix: pass volumes into the vector + test (~8 lines). Status: open.
- F-56: sub-gate duplication (`flay -m 30`): identical `TRANSFORMERS` ×3 (F-51 fix covers), identical `standard_deviation` in `pattern_matcher.rb:375`/`risk_manager.rb:445`, mirror pairs in ensemble, kbs_strategy, strategy_generator, store/portfolio. No bugs found in the pairs. Status: needs Dewayne's decision (readability only, like F-18).
- F-57 (improver C-1): `Config` doesn't coerce `lazy_update` (only Boolean left out of `coerce_types`), so `SQA_LAZY_UPDATE=0` or `"off"` turns lazy mode on and `Stock#should_update?` silently stops updates. Status: issue [#66](https://github.com/MadBomber/sqa/issues/66) (1 line + test).
- F-58 (improver): `Config#from_file` → `apply_incoming` (`config.rb:221-228`) skips coercion: JSON `"debug": "false"` → `debug? == true`; JSON/TOML `log_level` loads as String. Status: needs Dewayne's decision (coerce file values too; changes `log_level` type from JSON/TOML).
- F-59 (improver C-2): `PatternMatcher#update_cluster_centroids` (`pattern_matcher.rb:202-210`) gives every empty cluster `previous_centroids[0]`, not its own, contradicting the comment; clusters silently collapse. Fix: `each_with_index`, `previous_centroids[idx]` + test via `send`. Status: **PR open** [#84](https://github.com/MadBomber/sqa/pull/84) (also sizes clusters by `centroids.size` so short data does not crash).
- F-60 (improver C-3): Stooq and Alpha Vantage raise bare `RuntimeError "Bad Response: N"` on non-200 (`stooq.rb:77-79`, `alpha_vantage.rb:97-99`); Yahoo/FMP raise `ApiError` as `stock.rb:430` documents. ~2 lines + 2 tests. Status: open (after #58 merges; same lines).
- F-61 (improver C-4): `PortfolioOptimizer` `max_weight`/`min_weight` not honoured: `random_weights` clamps then renormalizes (886/1000 samples break `max_weight: 0.4`); no test covers constraints. Status: needs Dewayne's decision (redistribute vs reject; raise on infeasible bounds).
- F-62 (improver): `SeasonalAnalyzer.detect_seasonality?` threshold (stddev > 2.0 on average *daily* % returns per month) never fires on real data (54 years of F: 0.093), so `StrategyGenerator`'s seasonal branch is dead. Status: needs Dewayne's decision (monthly returns vs recalibrate).
- F-63 (improver): small no-ops: `bollinger_bands.rb:24` bare `middle.last`; `volume_breakout.rb:48,50` comparisons always true. Status: open (too thin alone; ride along with a nearby fix).

Dead-code scan still at develop 5ea98c8 (no new commits since the 05:45 scan).

### 2026-11-13 03:45 (seventh visit; security audit + improver, develop 93c16a1)

Security audit: bundle-audit clean; nothing else public. S-2 was fixed by #58 (merged).

- S-3: security finding, details held locally. Status: open.
- S-4: security finding, details held locally. Status: open.
- F-64 (improver I-1): `docs/advanced/fpop.md` treats `fpl_analysis` (Array of per-bar Hashes) as one Hash: Quick Start `analysis[:risk]` raises TypeError; documented keys `fpl/reward/quality_score` don't exist; wrong worked example, default `fpop` (14 not 10), DataFrame section, missing `:UNCERTAIN`; `fpop.rb:20-25` header example wrong. Status: issue [#80](https://github.com/MadBomber/sqa/issues/80).
- F-65 (improver I-2): `FPOP.determine_direction` (`fpop.rb:107-118`) returns `:FLAT` for `(0.0, 5.0)` and `(-3.0, 0.0)`; 482 F.csv bars mislabeled, so `StrategyGenerator` `required_fpl_directions: [:UP]` drops them. Status: issue [#81](https://github.com/MadBomber/sqa/issues/81).
- F-66 (improver I-3): `bin/sqa-summary` banner/header/examples still say `sqa-summary.rb`. Status: issue [#82](https://github.com/MadBomber/sqa/issues/82).
- F-67 (improver I-6): `Stock.new(source: :typo)` raises `NameError: uninitialized constant SQA::DataFrame::Typo` (`stock.rb:114-115`); should raise `BadParameterError` listing known sources. ~6 lines + test. Status: open (land after #70, which fixes `:yahoo_finance` at the same lines).
- F-68 (improver): `MarketRegime` accuracy set: `detect_history` labels each regime with the next window's volatility; windows are `window + 1` bars; `detect_trend_with_score` always uses `last(60)` as long SMA regardless of `lookback:`; `detect_strength` counts unchanged days as down. Status: needs Dewayne's decision (fix as a set; see inbox).
- F-69 (improver): CHANGELOG `[Unreleased]` presents `bin/sqa-recent-prices`, `sqa-summary`, `sqa-yf-fetch` as CLI utilities, but `spec.executables` lists only `sqa-console` and `spec.files` excludes `bin/`. Status: needs Dewayne's decision (ship them as executables, or reword CHANGELOG).
- F-70 (improver): `examples/download_prices.rb` (262 lines) likely redundant after #71 deletes F.csv and `bin/sqa-yf-fetch` writes `sqa.db`. Status: needs Dewayne's decision (remove vs. keep with pointer).
- F-71 (builder #84): `PatternMatcher#cluster_patterns` comment says "iterate until convergence" but always runs 10 iterations; `patterns.sample` unseeded; `extract_all_patterns` skips the last window (should be `size - pattern_length + 1`). Status: open.
- F-72 (builder #84): `test/test_helper.rb` uses deprecated `SimpleCov.add_group` (warns every run; use `SimpleCov.group`). Status: open (test-only, low-risk candidate).
- F-73 (triage): `.semver` (0.0.30) is tracked and shipped in `spec.files` while `lib/sqa/version.rb` is 0.4.0 and RubyGems has 0.0.38. Status: needs Dewayne's decision (delete `.semver`; when to cut 0.4.0).
- F-74 (builder #85): `bin/sqa-console` does `require "bundler/setup"`, so the installed executable likely raises `Bundler::GemfileNotFound` outside a project with a Gemfile. Unverified. Status: open (verify, then drop or guard the require).

Dead-code scan still at develop 5ea98c8 (not rerun this visit).
