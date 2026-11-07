# Findings: dev

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | No Gemfile, so bundle-audit n/a. Shell-outs, env and file reads read by hand. 2 findings, details held locally. |
| dead code | 2026-11-07 | Second-run cheap scan a9752c6..08300eb (README only); nothing new. |
| dependencies | never | |
| adoption | n/a | Skipped for dev by spec. |
| quality | 2026-11-07 | First run. RuboCop Lint+Security (TargetRubyVersion 3.4), flog, flay over copies of every .loki and .irbrc. F-15 to F-17. |

Dead-code scan last covered commit: 08300eb (origin/develop, 2026-11-07, second run)

## Findings

### 2026-11-06 (security audit + dead-code scan)

- **F-1:** `schedule.loki` with `lib/` backends, `test/` and `demo/` duplicated asgard's built-in scheduler (promoted in asgard 0.4.0) and failed to load under asgard 0.6.0 (`invoke_command` is now a reserved helper). Nothing imports it. Status: merged ([dev#1](https://github.com/MadBomber/dev/pull/1), 2026-11-07).
- **F-2:** the repo has no CI workflow. Its suite would become a clean load of every `.loki` through asgard once F-1 lands (no `test/` left). Status: open, needs Dewayne's decision (add a small load-check workflow, which needs asgard installed in CI, or leave it with no CI).
- **F-3:** `status_report.loki` `shell_history_collect` keeps lines that start with the date (`l.start_with?(date)`), but bash with `HISTTIMEFORMAT` writes `#<epoch>` comment lines before each command in `~/.bash_history`, not a date prefix. The collector may never match. Status: verified 2026-11-07, needs Dewayne's decision (blocked on S-1; see below).
- **F-4:** the README Contents table leaves out `branch_report.loki` and `db_doc.loki`. Status: merged ([dev#2](https://github.com/MadBomber/dev/pull/2), 2026-11-07).
- **S-1:** security finding, details held locally. Status: needs Dewayne's decision.
- **S-2:** security finding, details held locally. Status: open, low.

### 2026-11-07 (no audit; cheap scan, F-3 verified, review loop on dev#2)

- **F-3 update:** verified. Bash 5.3 with `HISTTIMEFORMAT` set writes `#<epoch>` on its own line before each command, so `l.start_with?(date)` never matches and the collector always SKIPs. The fix (pair each `#<epoch>` with the next line, compare the local date) is small, but it would start feeding shell history into the daily notes and the weekly `claude -p` prompt, so it waits on the S-1 decision. Status: needs Dewayne's decision.
- **F-5:** `branch_report.loki`, `db_doc.loki` and `.irbrc` begin with header comments naming another project's path (the vbr project it was copied from), and `db_doc.loki` says "Imported from this repo's .loki". Status: open (small cleanup PR once F-6 is decided).
- **F-6:** `db_doc.loki` (default DSN `vbr_development`, devcontainer guard) and `.irbrc` (vbr console helpers, default STI `Artifact`) are project-specific, against the README rule "Tasks here must stay universal". The README intro also says the repo has "no application code" and doesn't mention `.irbrc`. Status: needs Dewayne's decision (generalize them, move them back to the vbr repo, or keep them and document `.irbrc`).
- **F-7:** `branch_report.loki` skips only the bare default-branch name (`next if row[:name] == default_name`), so `origin/<default>` can show up in the "merged" and "no activity" lists. Status: PR'd ([dev#3](https://github.com/MadBomber/dev/pull/3)); verified 2026-11-07 with a fixture whose `origin/main` was 60 days idle.
- **F-8:** `db_doc_guard` always checks `pg_isready -h localhost -p 5432`, even when `DB_DOC_DSN` points somewhere else. Status: open, low (it ties into F-6).
- **F-9:** `branch_report`'s idle days use integer division and a strict `>`, so a branch is listed only after DAYS+1 whole days. Status: open, cosmetic.

### 2026-11-07, second run (quality audit; findings from the dev#3 review)

- **F-10:** `branch_default?` (dev#3) only skips `origin`'s copy of the default branch. In a fork, `upstream/main` can still show up in the merged and idle lists. The whole report is scoped to `origin` (`branch_default_name` and `branch_resolvable_base` hardcode it too), so a fix would mean deciding how other remotes are handled. Status: open, low.
- **F-11:** `branch_report` measures merge state and ahead/behind against the local default branch when one exists. After `git fetch` without `git pull`, a branch merged on the server shows "Merged: no", and so does `origin/main` itself. Verified 2026-11-07 with a fixture (`origin/feat` fast-forwarded into `origin/main`, local `main` behind). Comparing remote rows against `origin/<default>` would fix it. Status: open (verified; a good next PR).
- **F-12:** every branch in "Merged more than N day(s) ago" also appears in "No activity for over N day(s)", because the stale check has no `!merged` condition. The README row ("then the ones idle (or merged and idle)") reads as either intent. Status: needs Dewayne's decision (keep the overlap or make the lists disjoint).
- **F-13:** `branch_rows` splits `for-each-ref` output on `|`, so an author name containing `|` is truncated and breaks the Markdown table. Status: open, low.
- **F-14:** a ref with an empty committer date parses to epoch 0 and reports about 20,000 idle days. Status: unverified (no realistic ref found that triggers it).
- **F-15:** `test_check` (quality.loki) and `brakeman_check`, `rails_best_practices_check` and `active_record_doctor_check` (quality_rails.loki) repeat the same shape: run a command, write `*_output.txt`, find a summary line, print it, return `:pass` or `:fail` (flay mass 102). One shared helper would remove about 20 lines. Status: open.
- **F-16:** `branch_report` (flog 104.7, the highest in the repo) builds its two summary lists with the same expression (flay mass 54). A small list-rendering helper would shorten it and could be tested on its own. Status: open, low.
- **F-17:** `quality.loki` defines `STATUS_BADGES` inside a block (`Lint/ConstantDefinitionInBlock`). It still lands on `Tasks` lexically, so this is cosmetic. Otherwise RuboCop Lint and Security are clean under a 3.4 parser. Status: open, cosmetic.
