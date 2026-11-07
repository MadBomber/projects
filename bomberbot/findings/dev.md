# Findings: dev

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | No Gemfile, so bundle-audit n/a. Shell-outs, env and file reads read by hand. 2 findings, details held locally. |
| dead code | 2026-11-07 | Cheap scan c5f8967..a9752c6 (only #1's removal and README edits); nothing new. |
| dependencies | never | |
| adoption | n/a | Skipped for dev by spec. |
| quality | never | |

Dead-code scan last covered commit: a9752c6 (origin/develop, 2026-11-07)

## Findings

### 2026-11-06 (security audit + dead-code scan)

- **F-1:** `schedule.loki` with `lib/` backends, `test/` and `demo/` duplicated asgard's built-in scheduler (promoted in asgard 0.4.0) and failed to load under asgard 0.6.0 (`invoke_command` is now a reserved helper). Nothing imports it. Status: merged ([dev#1](https://github.com/MadBomber/dev/pull/1), 2026-11-07).
- **F-2:** the repo has no CI workflow. Its suite would become a clean load of every `.loki` through asgard once F-1 lands (no `test/` left). Status: open, needs Dewayne's decision (add a small load-check workflow, which needs asgard installed in CI, or leave it with no CI).
- **F-3:** `status_report.loki` `shell_history_collect` keeps lines that start with the date (`l.start_with?(date)`), but bash with `HISTTIMEFORMAT` writes `#<epoch>` comment lines before each command in `~/.bash_history`, not a date prefix. The collector may never match. Status: verified 2026-11-07, needs Dewayne's decision (blocked on S-1; see below).
- **F-4:** the README Contents table leaves out `branch_report.loki` and `db_doc.loki`. Status: PR'd ([dev#2](https://github.com/MadBomber/dev/pull/2)).
- **S-1:** security finding, details held locally. Status: needs Dewayne's decision.
- **S-2:** security finding, details held locally. Status: open, low.

### 2026-11-07 (no audit; cheap scan, F-3 verified, review loop on dev#2)

- **F-3 update:** verified. Bash 5.3 with `HISTTIMEFORMAT` set writes `#<epoch>` on its own line before each command, so `l.start_with?(date)` never matches and the collector always SKIPs. The fix (pair each `#<epoch>` with the next line, compare the local date) is small, but it would start feeding shell history into the daily notes and the weekly `claude -p` prompt, so it waits on the S-1 decision. Status: needs Dewayne's decision.
- **F-5:** `branch_report.loki`, `db_doc.loki` and `.irbrc` begin with header comments naming another project's path (the vbr project it was copied from), and `db_doc.loki` says "Imported from this repo's .loki". Status: open (small cleanup PR once F-6 is decided).
- **F-6:** `db_doc.loki` (default DSN `vbr_development`, devcontainer guard) and `.irbrc` (vbr console helpers, default STI `Artifact`) are project-specific, against the README rule "Tasks here must stay universal". The README intro also says the repo has "no application code" and doesn't mention `.irbrc`. Status: needs Dewayne's decision (generalize them, move them back to the vbr repo, or keep them and document `.irbrc`).
- **F-7:** `branch_report.loki` skips only the bare default-branch name (`next if row[:name] == default_name`), so `origin/<default>` can show up in the "merged" and "no activity" lists. Status: open (unverified against a repo whose `origin/main` is over 30 days idle; reasoned from the code).
- **F-8:** `db_doc_guard` always checks `pg_isready -h localhost -p 5432`, even when `DB_DOC_DSN` points somewhere else. Status: open, low (it ties into F-6).
- **F-9:** `branch_report`'s idle days use integer division and a strict `>`, so a branch is listed only after DAYS+1 whole days. Status: open, cosmetic.
