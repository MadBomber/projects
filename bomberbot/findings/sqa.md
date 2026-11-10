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
| quality | 2026-11-10 | First run at develop 3209f5f. rubocop lib+test clean except the 6 F-12 Lint/UselessAssignment (fixed by #50, now 0 offenses on develop 795bce1). flog gate passes (no method ≥50; 16 in 40–50 warn band, highest 46.6). flay gate passes (no dup mass ≥50). Only assertion-free test was F-12's. New: F-17 reek_check gate not self-contained, F-18 detect_double_top/_bottom mirror near-dup. |

Dead-code scan last covered commit: 7fa65e3 (develop, 2026-11-10; `lib/` only, brief)

## Findings

### 2026-11-10 (first visit; security audit + brief dead-code pass + improver, develop 7fa65e3)

- S-1: security finding, details held locally. Status: PR'd [#44](https://github.com/MadBomber/sqa/pull/44) + needs Dewayne's decision on the rest.
- S-2: security finding, details held locally. Status: open (low; small fix, candidate next visit).
- F-1: five runtime deps never loaded (`redis`, `ruby_llm`, `shared_tools`, `eps`, `regent`; `sqa.gemspec:51-60`). `redis` comes in for kbs, which `require`s it eagerly but lists it only as a dev dep. Status: needs Dewayne's decision (gemspec; downstream may rely on them transitively).
- F-2: `SQA::PluginManager` (`lib/sqa/plugin_manager.rb`) is never required; `Config#inject_additional_properties` has no caller. Status: needs Dewayne's decision (wire up vs remove; public API).
- F-3: top-level `AlphaVantageAPI` (`lib/api/alpha_vantage_api.rb`, 458 lines) is loaded by `require 'sqa'` but unused anywhere in sqa or the local dependents. Status: needs Dewayne's decision (deprecate/remove/keep).
- F-4: unreachable Ruby 1.9 branch and dead `|| const_missing` in `String#to_constant` (`lib/patches/string.rb:42-46`). Status: PR open [#52](https://github.com/MadBomber/sqa/pull/52) (Fixes #47; lib change, for Dewayne).
- F-5: gem ships repo clutter (`spec.files` = git ls-files minus a few dirs: 254 files, ~7 MB; docs/, examples/rails_app, checksums/, notes, `trace.log`, `main.just`). Status: needs Dewayne's decision (gemspec allowlist is never low risk).
- F-6: `changelog_uri` = homepage (`sqa.gemspec:26`). Status: open (gemspec; fold into a gemspec PR).
- F-7: suite red on develop without `FMP_API_KEY`: `test/data_frame/fmp_test.rb` 3F/4E because the key is read before the stubbed request. Status: merged (low risk) [#45](https://github.com/MadBomber/sqa/pull/45), develop 3209f5f.
- F-8: `required_ruby_version >= 3.2` but sqa-tai `~> 0.4` needs `>= 3.4`; docs say Ruby 3.2+/3.3.6. Status: open (gemspec + docs; follows debug_me#23 precedent).
- F-9: README examples don't match the API (Portfolio `load_from_csv`/`save_trades_to_csv`/summary keys, backtest fractions printed as %, nonexistent `MomentumBreakout`/`MeanReversion`, `Config.new(data_dir:)`). Status: PR open [#46](https://github.com/MadBomber/sqa/pull/46). `holding_period:` (README:411) and `gem install sqa-cli` (README:102) need Dewayne's decision.
- F-10: CHANGELOG `[Unreleased]` misses SQLite stores (b185b83), Yahoo window (7838c44), new bin scripts and rename, dependency changes (7411d07), nil guards (89e02af). Status: merged (low risk) [#51](https://github.com/MadBomber/sqa/pull/51), develop 795bce1 (Fixes #49).
- F-11: no docs page mentions `sqa.db`/`portfolio.db`/`SQA::Store`; concepts/file_formats still describe per-ticker CSV/JSON. Status: open (docs deploy from develop, so not low risk).
- F-12: `test_on_signal_callback` (test/stream_test.rb:77-92) asserts nothing; unused var in gp_test.rb. Fixing clears all 6 RuboCop offenses. Status: merged (low risk) [#50](https://github.com/MadBomber/sqa/pull/50), develop 9cee34f (Fixes #48); repo now lint-clean on `lib test`.
- F-13: unused `$data` fixture in `test/test_helper.rb:75-99`. Status: open (test-only removal).
- F-14: version numbers disagree: `VERSION = '0.4.0'`, CHANGELOG top release `[0.1.0]` above `[0.3.0]`, `.semver` 0.0.30, RubyGems 0.0.38. Status: needs Dewayne's decision.
- F-15: `SQA.init(argv)` discards its argument though YARD advertises `--debug --verbose` (`lib/sqa/init.rb:35-37`). Status: needs Dewayne's decision (taste rule says fix code; small design choice).
- F-16: no test CI workflow; needs TA-Lib C lib, Ruby 3.4/4.0 matrix (polars-df has no 4.1 prebuilt). Status: open (after F-7 merges).

### 2026-11-10 (second visit, resumed; quality audit, develop 3209f5f)

- F-17: `rake reek_check` (Rakefile:135-190) is not self-contained in a clean checkout. `git ls-files | grep -iE 'reek|\.quality'` tracks neither `.reek.yml` nor `.quality/reek_baseline.txt`. Two defects: (a) with no `.reek.yml` resolving up-tree the task passes `configuration: nil` to `Reek::Examiner.new`, which raises on reek 6.x (`undefined method 'directive_for' for nil`); (b) it reads an untracked `.quality/reek_baseline.txt`, so an empty baseline marks every smelly lib file NEW and the gate aborts. Works in Dewayne's workspace (shared `.reek.yml` up-tree) and no CI runs it (F-16), so it only bites a fresh clone / contributor / future CI. The Rakefile is shared asgard-style tooling copied across the sqa gems, so a fix should be consistent. Status: needs Dewayne's decision (shared tooling + baseline generation is his call). Overlaps F-16.
- F-18: `PatternMatcher#detect_double_top` / `#detect_double_bottom` (`lib/sqa/pattern_matcher.rb:386-420, 422-453`) are mirror-image near-duplicates (the file's two highest-complexity methods, flog 46.6 each), differing only in peaks↔valleys and min↔max/drop↔rise; `detect_head_shoulders` (455) and `detect_triangle` (486) share the same scaffolding. flay doesn't flag it (below mass 50). A DRY extraction parameterizing direction is a readability judgment call, not a provable defect — flog/flay gates already pass. Status: needs Dewayne's decision.

Dead-code scan last covered commit: 7fa65e3 (develop, 2026-11-10; `lib/` only, brief). Not re-run this visit; quality audit ran instead.
