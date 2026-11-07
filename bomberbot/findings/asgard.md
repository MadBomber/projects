# Findings: asgard

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | bundle-audit clean; read of shell-outs, loads, env, file writes; one finding (S-1) |
| dead code | never | quick scans only (2026-11-06, 2026-11-07), see F-3/F-4/F-8 |
| dependencies | 2026-11-07 | bundle-audit clean; bundle outdated lists only transitive gems (F-6); Ruby 4 greps clean apart from F-8; one locale defect in lib (F-7) |
| adoption | never | |
| quality | never | `bin/asgard quality` ran green apart from Fasterer/Reek warnings (2026-11-06) |

Dead-code scan last covered commit: ddc2b76 (develop, 2026-11-07)

## Findings

### 2026-11-06 (security audit, plus cheap scans)

- **S-1**: security finding, details held locally. Status: PR'd ([#6](https://github.com/MadBomber/asgard/pull/6)).
- **F-2**: with `LANG`/`LC_ALL` unset, the suite has one error. `TestRegressionCoreReview#test_regression_r8_run_reports_a_runtime_cycle` raises `ArgumentError: invalid byte sequence in US-ASCII` because `Open3.capture3` output is tagged US-ASCII and the assertion matches a UTF-8 em dash. It reproduces on develop 902cdaf. It affects launchd/cron runs (such as bomberbot's) and watcher runs started without a locale. Likely fix: force the encoding of captured output in `test/test_helper.rb` `run_asgard`. Status: PR'd ([#7](https://github.com/MadBomber/asgard/pull/7)).
- **F-3**: `Asgard::Doctor.duplicate_methods` (`lib/asgard/doctor.rb`) isn't called anywhere in lib. It has been unused since it was added in ede783b, when `Doctor::TaskSections` took over override reporting. Only `TestDoctorDuplicateMethods` (3 tests) uses it. It is a public class method on an internal diagnostics class and is not in `sig/` or the docs. Status: needs Dewayne's decision (is `Asgard::Doctor` public API? If not, delete the method and its 3 tests). Rechecked 2026-11-07 on ddc2b76: its tests are still its only callers.
- **F-4**: reek reports `Schedule::Table#state_color` and `#last_exit_color` as unused private methods. False positive: they are called dynamically via `send(:"#{column}_color", ...)` in `Table#styled`. Status: rejected (not dead).
- **F-5**: the `bin/asgard quality` ERBLint gate globs `**/*.erb`, so it picks up gems vendored under `vendor/bundle` and then fails because `erb_lint` isn't in the Gemfile. It only hits clones that vendor their bundle. Likely fix: exclude `vendor/` in `erb_lint_check`'s glob. Status: open (low value).

### 2026-11-07 (dependencies audit, plus cheap scans)

- **F-6**: `bundle outdated` lists only transitive gems, each held back by another gem's constraint: bigdecimal 3.3.1 (latest 4.1.3; exhale `~> 3.1`), prism 1.9.0 (2.1.0; archspec `~> 1.9.0`), herb 0.11.1 (0.11.2; archspec `< 0.11.2`), and unicode-display_width 2.6.0 (3.3.0; strings, via tty-table, `< 3.0`). None of them is a direct dependency. Every gemspec runtime constraint (thor, dotenv, tty-table, ractor_queue, listen, tsort) resolves to the current release. `Gemfile.lock` isn't tracked. bundle-audit is clean. Status: rejected (nothing to do in this repo).
- **F-7**: with no locale set (launchd, cron; `Schedule.environment` passes only PATH plus the user's env), Ruby's `Encoding.default_external` is US-ASCII. So the `ps` output that `lib/asgard/pid.rb` reads through `Open3` is tagged US-ASCII. `Pid.group_commands` calls `line.strip.split(/\s+/, 2)`, and `Pid.matches?` calls `command.include?(word)`. Both raise `Encoding::CompatibilityError` (invalid byte sequence in US-ASCII) once any process on the machine has a non-ASCII command line, such as a path with an accented letter. That breaks the jobs and watcher liveness checks under a scheduled asgard. Reproduced outside the suite: under `env -u LANG -u LC_ALL`, `strip` on `Open3.capture2` output that contains `café` raises. Likely fix: tag the `ps` output UTF-8 and `scrub` it inside `Pid`. That is one place, and it is testable through the existing `command:` injection. The broader option, `Encoding.default_external = UTF_8` in `bin/asgard`, changes what every user task sees. Found by `/code-review` on #7. Status: open (best next PR).
- **F-8**: `Watch::Daemon.join_worker` (`lib/asgard/watch/daemon.rb`) falls back to `ractor.take` when a Ractor doesn't respond to `join`. With `required_ruby_version >= 4.0`, that branch can't run: Ruby 4.0.9's `Ractor` has `join` and `value` and no `take`. The only caller is `Daemon` itself, plus `test_regression_w3_join_worker_tolerates_a_dead_worker`, which uses real Ractors. Fix: drop the `respond_to?` check and the fallback. Status: open (small removal).
- Dead-code quick scan of 902cdaf..ddc2b76 (PR #6 only): `StateDir.private_dir` is called by both schedule backends and by `StateDir` itself. Nothing removable.
