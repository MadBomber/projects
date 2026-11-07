# Findings: dev

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | No Gemfile, so bundle-audit n/a. Shell-outs, env and file reads read by hand. 2 findings, details held locally. |
| dead code | 2026-11-06 | Cheap scan done with the security audit; found F-1. |
| dependencies | never | |
| adoption | n/a | Skipped for dev by spec. |
| quality | never | |

Dead-code scan last covered commit: c5f8967 (origin/develop, 2026-11-06)

## Findings

### 2026-11-06 (security audit + dead-code scan)

- **F-1:** `schedule.loki` with `lib/` backends, `test/` and `demo/` duplicated asgard's built-in scheduler (promoted in asgard 0.4.0) and failed to load under asgard 0.6.0 (`invoke_command` is now a reserved helper). Nothing imports it. Status: PR'd ([dev#1](https://github.com/MadBomber/dev/pull/1)).
- **F-2:** the repo has no CI workflow. Its suite would become a clean load of every `.loki` through asgard once F-1 lands (no `test/` left). Status: open, needs Dewayne's decision (add a small load-check workflow, which needs asgard installed in CI, or leave it with no CI).
- **F-3:** `status_report.loki` `shell_history_collect` keeps lines that start with the date (`l.start_with?(date)`), but bash with `HISTTIMEFORMAT` writes `#<epoch>` comment lines before each command in `~/.bash_history`, not a date prefix. The collector may never match. Status: unverified (check against a real history file).
- **F-4:** the README Contents table leaves out `branch_report.loki` and `db_doc.loki`. Status: open (small docs PR).
- **S-1:** security finding, details held locally. Status: needs Dewayne's decision.
- **S-2:** security finding, details held locally. Status: open, low.
