# Findings: asgard

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | bundle-audit clean; read of shell-outs, loads, env, file writes; one finding (S-1) |
| dead code | never | quick debride scan only (2026-11-06), see F-3/F-4 |
| dependencies | never | |
| adoption | never | |
| quality | never | `bin/asgard quality` ran green apart from Fasterer/Reek warnings (2026-11-06) |

Dead-code scan last covered commit: 902cdaf (develop, 2026-11-06)

## Findings

### 2026-11-06 (security audit, plus cheap scans)

- **S-1**: security finding, details held locally. Status: PR'd ([#6](https://github.com/MadBomber/asgard/pull/6)).
- **F-2**: with `LANG`/`LC_ALL` unset, the suite has one error. `TestRegressionCoreReview#test_regression_r8_run_reports_a_runtime_cycle` raises `ArgumentError: invalid byte sequence in US-ASCII` because `Open3.capture3` output is tagged US-ASCII and the assertion matches a UTF-8 em dash. It reproduces on develop 902cdaf. It affects launchd/cron runs (such as bomberbot's) and watcher runs started without a locale. Likely fix: force the encoding of captured output in `test/test_helper.rb` `run_asgard`. Status: open.
- **F-3**: `Asgard::Doctor.duplicate_methods` (`lib/asgard/doctor.rb`) isn't called anywhere in lib. It has been unused since it was added in ede783b, when `Doctor::TaskSections` took over override reporting. Only `TestDoctorDuplicateMethods` (3 tests) uses it. It is a public class method on an internal diagnostics class and is not in `sig/` or the docs. Status: needs Dewayne's decision (is `Asgard::Doctor` public API? If not, delete the method and its 3 tests).
- **F-4**: reek reports `Schedule::Table#state_color` and `#last_exit_color` as unused private methods. False positive: they are called dynamically via `send(:"#{column}_color", ...)` in `Table#styled`. Status: rejected (not dead).
- **F-5**: the `bin/asgard quality` ERBLint gate globs `**/*.erb`, so it picks up gems vendored under `vendor/bundle` and then fails because `erb_lint` isn't in the Gemfile. It only hits clones that vendor their bundle. Likely fix: exclude `vendor/` in `erb_lint_check`'s glob. Status: open (low value).
