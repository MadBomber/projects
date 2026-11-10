# Findings: sqa

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-10 | First run, at develop 7fa65e3. bundle-audit clean (no lockfile committed; resolved in scratch). SQL bind params, YAML safe, curl shell-out escaped. S-1, S-2 held locally. |
| dead code | 2026-11-10 (lib only, brief) | debride over `lib/` at 7fa65e3: F-1 unused runtime deps, F-2 PluginManager never required, F-3 top-level `AlphaVantageAPI` unused, F-4 dead Ruby 1.9 branch. test/examples/docs not covered. |
| dependencies | n/a | Not yet run. Noted: floor `>= 3.2` but sqa-tai 0.4 needs `>= 3.4` (F-8); polars-df has no prebuilt gem for 4.1-preview, so the suite runs on 4.0.9. |
| adoption | n/a | Not yet run. Improver noted README examples drift (F-9), CHANGELOG gaps for the SQLite store work (F-10), docs never mention SQLite storage (F-11). `docs/llms.txt` exists. |
| quality | n/a | Not yet run. `.rubocop.yml` exists; 6 `Lint/UselessAssignment` offenses in test/stream_test.rb + test/gp_test.rb (F-12). |

Dead-code scan last covered commit: 7fa65e3 (develop, 2026-11-10; `lib/` only, brief)

## Findings

### 2026-11-10 (first visit; security audit + brief dead-code pass + improver, develop 7fa65e3)

- S-1: security finding, details held locally. Status: PR'd [#44](https://github.com/MadBomber/sqa/pull/44) + needs Dewayne's decision on the rest.
- S-2: security finding, details held locally. Status: open (low; small fix, candidate next visit).
- F-1: five runtime deps never loaded (`redis`, `ruby_llm`, `shared_tools`, `eps`, `regent`; `sqa.gemspec:51-60`). `redis` comes in for kbs, which `require`s it eagerly but lists it only as a dev dep. Status: needs Dewayne's decision (gemspec; downstream may rely on them transitively).
- F-2: `SQA::PluginManager` (`lib/sqa/plugin_manager.rb`) is never required; `Config#inject_additional_properties` has no caller. Status: needs Dewayne's decision (wire up vs remove; public API).
- F-3: top-level `AlphaVantageAPI` (`lib/api/alpha_vantage_api.rb`, 458 lines) is loaded by `require 'sqa'` but unused anywhere in sqa or the local dependents. Status: needs Dewayne's decision (deprecate/remove/keep).
- F-4: unreachable Ruby 1.9 branch and dead `|| const_missing` in `String#to_constant` (`lib/patches/string.rb:42-46`). Status: open (small PR; obvious yes).
- F-5: gem ships repo clutter (`spec.files` = git ls-files minus a few dirs: 254 files, ~7 MB; docs/, examples/rails_app, checksums/, notes, `trace.log`, `main.just`). Status: needs Dewayne's decision (gemspec allowlist is never low risk).
- F-6: `changelog_uri` = homepage (`sqa.gemspec:26`). Status: open (gemspec; fold into a gemspec PR).
- F-7: suite red on develop without `FMP_API_KEY`: `test/data_frame/fmp_test.rb` 3F/4E because the key is read before the stubbed request. Status: PR'd (see runs.md).
- F-8: `required_ruby_version >= 3.2` but sqa-tai `~> 0.4` needs `>= 3.4`; docs say Ruby 3.2+/3.3.6. Status: open (gemspec + docs; follows debug_me#23 precedent).
- F-9: README examples don't match the API (Portfolio `load_from_csv`/`save_trades_to_csv`/summary keys, backtest fractions printed as %, nonexistent `MomentumBreakout`/`MeanReversion`, `Config.new(data_dir:)`). Status: PR'd (see runs.md). `holding_period:` (README:411) and `gem install sqa-cli` (README:102) need Dewayne's decision.
- F-10: CHANGELOG `[Unreleased]` misses SQLite stores (b185b83), Yahoo window (7838c44), new bin scripts and rename, dependency changes (7411d07), nil guards (89e02af). Status: open (docs; easy yes).
- F-11: no docs page mentions `sqa.db`/`portfolio.db`/`SQA::Store`; concepts/file_formats still describe per-ticker CSV/JSON. Status: open (docs deploy from develop, so not low risk).
- F-12: `test_on_signal_callback` (test/stream_test.rb:77-92) asserts nothing; unused var in gp_test.rb. Fixing clears all 6 RuboCop offenses. Status: open (test-only; easy yes).
- F-13: unused `$data` fixture in `test/test_helper.rb:75-99`. Status: open (test-only removal).
- F-14: version numbers disagree: `VERSION = '0.4.0'`, CHANGELOG top release `[0.1.0]` above `[0.3.0]`, `.semver` 0.0.30, RubyGems 0.0.38. Status: needs Dewayne's decision.
- F-15: `SQA.init(argv)` discards its argument though YARD advertises `--debug --verbose` (`lib/sqa/init.rb:35-37`). Status: needs Dewayne's decision (taste rule says fix code; small design choice).
- F-16: no test CI workflow; needs TA-Lib C lib, Ruby 3.4/4.0 matrix (polars-df has no 4.1 prebuilt). Status: open (after F-7 merges).
