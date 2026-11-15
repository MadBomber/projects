# Findings: dev

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-15 | Fourth run, at 8113aab, focused on the new code 6d0bb59..8113aab (aws, fake_aws, rails/db, rails/dev, runbook, client/ticket, gem_tasks release gating): F-57 to F-59, S-9 to S-13 held locally. Earlier: Third run, at 657e6bd, focused on dev#10–#12 (STATUS_WEEK_OF, reek_baseline, SKIP paths); fixtures with asgard 0.6.0, reek 6.6.0, global bundle-audit. F-52 verified, F-36 still holds; S-7, S-8 held locally. (Second run 2026-11-12 at 5a0b89e: F-40 to F-42, S-4 to S-6. First 2026-11-06: S-1, S-2.) |
| dead code | 2026-11-15 | Seventh-run cheap scan 6d0bb59..8113aab (dev#38–#47, ~3,600 lines); nothing new: every new helper has a caller, uncalled names are tasks. Earlier: Sixth-run cheap scan 657e6bd..6d0bb59 (dev#16–#34); nothing new. Fifth-run cheap scan 5a0b89e..657e6bd (dev#10 to #12); nothing new. (Previous: b1c29ed..5a0b89e, 2026-11-12.) |
| dependencies | 2026-11-15 | Third run, at 8113aab (aws, fake_aws, rails/db, rails/dev, runbook, client/ticket, gem_tasks since 657e6bd). New requires load on 4.0.9 and 4.1.0-preview1; inside an asgard-only bundle client/ticket's unused base64 require breaks every task (F-62). pg 18.7, jira-cli 1.7.0, gh, glow and gem flags all exist; docker emulator flags not checked (daemon down); AWS CLI not run by rule. asgard 0.6.1 is out and loads the files clean. Found F-62 to F-64; a Dockerfile recheck matched S-12. (Second run 2026-11-13 at 657e6bd: F-53. First 2026-11-12 at 5a0b89e: F-27, F-32 to F-36.) |
| adoption | n/a | Skipped for dev by spec. |
| quality | 2026-11-14 | Third run, at 6d0bb59, focused on dev#16–#22, #25, #26, #30, #31, #33, #34; RuboCop Lint+Security, flog, flay, debride, asgard 0.6.0 fixtures. F-54, F-55, F-16 update; triage added F-56. Earlier: Second run, at 657e6bd, focused on dev#10–#12. RuboCop Lint+Security (TargetRubyVersion 3.4) plus rubocop-strict.yml, flog, flay, reek 6.6.0 over .rb copies; asgard 0.6.0 fixtures for reek_baseline/reek_check, the SKIP paths and STATUS_WEEK_OF. F-47 to F-49, F-45 update. (First run 2026-11-07: F-15 to F-17.) |

Dead-code scan last covered commit: 8113aab (origin/develop == main, 2026-11-15, seventh run)

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
- **F-7:** `branch_report.loki` skips only the bare default-branch name (`next if row[:name] == default_name`), so `origin/<default>` can show up in the "merged" and "no activity" lists. Status: merged ([dev#3](https://github.com/MadBomber/dev/pull/3), 2026-11-07); verified 2026-11-07 with a fixture whose `origin/main` was 60 days idle.
- **F-8:** `db_doc_guard` always checks `pg_isready -h localhost -p 5432`, even when `DB_DOC_DSN` points somewhere else. Status: open, low (it ties into F-6).
- **F-9:** `branch_report`'s idle days use integer division and a strict `>`, so a branch is listed only after DAYS+1 whole days. Status: open, cosmetic.

### 2026-11-07, second run (quality audit; findings from the dev#3 review)

- **F-10:** `branch_default?` (dev#3) only skips `origin`'s copy of the default branch. In a fork, `upstream/main` can still show up in the merged and idle lists. The whole report is scoped to `origin` (`branch_default_name` and `branch_resolvable_base` hardcode it too), so a fix would mean deciding how other remotes are handled. Status: open, low.
- **F-11:** `branch_report` measures merge state and ahead/behind against the local default branch when one exists. After `git fetch` without `git pull`, a branch merged on the server shows "Merged: no", and so does `origin/main` itself. Verified 2026-11-07 with a fixture (`origin/feat` fast-forwarded into `origin/main`, local `main` behind). Comparing remote rows against `origin/<default>` would fix it. Status: PR'd ([dev#4](https://github.com/MadBomber/dev/pull/4), one origin-first baseline for every row).
- **F-12:** every branch in "Merged more than N day(s) ago" also appears in "No activity for over N day(s)", because the stale check has no `!merged` condition. The README row ("then the ones idle (or merged and idle)") reads as either intent. Status: needs Dewayne's decision (keep the overlap or make the lists disjoint).
- **F-13:** `branch_rows` splits `for-each-ref` output on `|`, so an author name containing `|` is truncated and breaks the Markdown table. Status: PR'd (stacked on dev#22; widened to `|` in branch names, which shifts every field and marks the branch stale).
- **F-14:** a ref with an empty committer date parses to epoch 0 and reports about 20,000 idle days. Status: unverified (no realistic ref found that triggers it).
- **F-15:** `test_check` (quality.loki) and `brakeman_check`, `rails_best_practices_check` and `active_record_doctor_check` (quality_rails.loki) repeat the same shape: run a command, write `*_output.txt`, find a summary line, print it, return `:pass` or `:fail` (flay mass 102). One shared helper would remove about 20 lines. Status: open.
- **F-16:** `branch_report` (flog 104.7, the highest in the repo) builds its two summary lists with the same expression (flay mass 54). A small list-rendering helper would shorten it and could be tested on its own. Status: open, low.
- **F-17:** `quality.loki` defines `STATUS_BADGES` inside a block (`Lint/ConstantDefinitionInBlock`). It still lands on `Tasks` lexically, so this is cosmetic. Otherwise RuboCop Lint and Security are clean under a 3.4 parser. Status: open, cosmetic.

### 2026-11-07, third run (no audit; findings from the dev#4 review)

- **F-18:** `branch_resolvable_base` checks `refs/remotes/origin/<name>` but returns the short name `origin/<name>` (and falls back to the short `<name>`). git resolves short names tags first, then heads, then remotes, so a local branch or tag named `origin/main` (or a tag `main`) would win, with "refname is ambiguous" on stderr. This was already true before dev#4. Status: open, low.
- **F-19:** when `origin/HEAD` is unset, `branch_default_name` looks only at local `main`/`master` and otherwise guesses `main`. It never checks `origin/main` or `origin/master`, and an unresolvable base gives `-` and "Merged: no" on every row without a warning. Status: open, low.
- **F-20:** `branch_merged?` starts a second git process per row, but `merge-base --is-ancestor name base` matches `ahead == 0` from `branch_ahead_behind`'s `rev-list --left-right --count`. Deriving it would halve the subprocesses and keep the two columns consistent. Status: open, low (efficiency).
- **F-21:** the report never fetches, and its header names `origin/<default>` without saying how old that ref is. Printing its committer date or the `FETCH_HEAD` mtime would show when the data is stale. Status: open, low.

### 2026-11-12 (dependencies audit; triage and improver findings)

Dewayne merged dev#5 and dev#6 on 2026-11-09 from `bomber/` branches whose visit wasn't recorded here. dev#5's body names **F-24** (top-level `require "base64"` in `status_report.loki` broke the whole file on Ruby 3.4+ bundles; status: merged, dev#5) and guarded a missing `claude`; dev#6 made the fasterer and Rails gates SKIP when their gem isn't bundled (merged). F-22, F-23 and F-25 were never written down and are not reused.

- **F-13 update:** since 1bf1c45, `split("|", 5)` keeps an author with `|` whole, so nothing is truncated, but the Markdown row still gets an extra cell. Fix: escape `|` in the author field. Status: PR'd (stacked on dev#22; see F-13).
- **F-6 update:** `.irbrc` is byte-identical to the copy in the vbr project, which is the one its consoles load; nothing loads dev's copy. Also see S-3. Status: needs Dewayne's decision.
- **F-26:** `reek_check` (quality.loki:196) tells users to run `asgard reek_baseline`, which doesn't exist in dev (`Could not find command "reek_baseline".` in pgdd, runsheets and a fixture), though about nine importing repos carry `.quality/reek_baseline.txt`. Status: PR'd (see open PRs in repos/dev.md).
- **F-27:** absent analysis tools fail instead of SKIP, against README "Checks whose tool or config is absent report SKIP": `flay_check`/`reek_check` `require` with no rescue, so a missing gem aborts the whole `quality` run with no summary; `flog_check` reports FAIL when flog is neither bundled nor on PATH; `bundler_audit_check` reports FAIL with no `Gemfile.lock`. Status: PR'd ([dev#12](https://github.com/MadBomber/dev/pull/12), stacked on dev#11).
- **F-28:** `weekly_report` treats `STATUS_WEEK_OF` as the window start even when it isn't a Monday (the header's own example, 2026-09-22, is a Tuesday), so Monday's daily summary is missed. Status: PR'd ([dev#10](https://github.com/MadBomber/dev/pull/10)).
- **F-29:** `claude_sessions_collect` (status_report.loki:211) maps only `/` and `_` to `-`, but Claude Code maps every non-alphanumeric character, so a repo path with a `.` never finds its sessions. 1-line fix, but it would feed more session text into the weekly `claude -p` prompt, so it waits on S-1 like F-3. Status: needs Dewayne's decision.
- **F-30:** `gem_tasks.loki` can't be imported without `quality.loki` (`release` has `depends_on [:quality, :build]` → `asgard: undefined task(s) in depends_on: quality`). Every current consumer imports both. Fix: `import "quality.loki"` at the top, or a README sentence. Status: PR'd ([dev#53](https://github.com/MadBomber/dev/pull/53), 2026-11-15); since f7049e8 `build` also depends on `quality`.
- **F-31:** `gem_version` reads the first `lib/**/version.rb`; with a nested `lib/foo/cli/version.rb` it returns nil and `build` makes `foo-.gem` (`release -y` would tag `v`). Status: issue [#7](https://github.com/MadBomber/dev/issues/7).
- **F-32:** `console` falls back to `bundle exec irb`, which fails on Ruby 4 when irb isn't in the bundle (irb is a bundled gem). Status: issue [#8](https://github.com/MadBomber/dev/issues/8).
- **F-33:** `flay_check`/`reek_check` load whichever flay/reek is newest in asgard's Ruby, not the lockfile's version (asgard never sets up Bundler), so the gate and `bundle exec reek` can disagree. Status: needs Dewayne's decision (subprocess via `bundle exec` like flog, or accept and document).
- **F-34:** `rubocop_check` and `test_check` FAIL when rubocop isn't bundled or there's no Gemfile. Status: needs Dewayne's decision (SKIP, or keep FAIL and narrow the README sentence).
- **F-35:** `db_doc_guard` blames Postgres when `pg_isready` itself is missing. Status: open, low (waits on F-6).
- **F-36:** the README lists no prerequisites (Ruby >= 4.0 via asgard 0.6.0; flog/flay/reek in asgard's own Ruby; macOS-only bits such as `open`, `brew install mkdocs`, `icalBuddy`). Status: issue [#27](https://github.com/MadBomber/dev/issues/27) (rechecked 2026-11-13 after F-27 merged; still holds).
- **F-37:** `branch_report` lets git's `fatal: ref refs/remotes/origin/HEAD is not a symbolic ref` reach stderr (Open3.capture2 doesn't capture it). 1-line fix. Status: PR'd ([dev#22](https://github.com/MadBomber/dev/pull/22); also silences the detached-HEAD line and aborts outside a git repo).
- **F-38:** `rubocop-strict.yml` and the README say its cops fire "regardless of .rubocop_todo.yml or inline directives"; inline disables still win, and it loses to the todo file if inherited first or under `inherit_mode: merge: [Exclude]`. Status: issue [#9](https://github.com/MadBomber/dev/issues/9).
- **F-39:** stale comments: `quality.loki:3-4` and `gem_tasks.loki:4` show `import "../dev/..."`/`repo_dev.loki` (README and consumers use `import_up "dev/..."`); `quality.loki:9,327-328` say Rails checks load "once Rails is defined" (actually the `RAILS_ROOT` guard); flog's desc says ">=20/>=50" but the code uses `>`; the `test_check` label says "Tests + Coverage" but nothing measures coverage. Status: open, cosmetic.
- **S-3:** security finding, details held locally. Status: needs Dewayne's decision, low.

### 2026-11-12, fifth run (security audit; improver findings)

No dead-code scan: `develop` is still 5a0b89e.

- **F-40:** `github_collect` (status_report.loki:147) runs `gh repo view` in the process cwd, while every other collector reads `RR` (`status_repo_root`), so with `RR` ≠ cwd the GitHub section reports on the wrong repo or SKIPs. Fix: `chdir: status_repo_root` through `safe_capture2`. Status: issue [#15](https://github.com/MadBomber/dev/issues/15).
- **F-41:** `weekly_report` (status_report.loki:327) passes the whole prompt as one argv entry and rescues only `Errno::ENOENT`; a week over ARG_MAX crashes (`Argument list too long - claude (Errno::E2BIG)`, verified with 1.2 MB of daily files). Fix: prompt on stdin. Status: open, low; ship together with the S-4 fix.
- **F-42:** `db_doc`, `db_doc_diff`, `db_doc_mermaid` (db_doc.loki:29,36,47) put `DB_DOC_DSN` unquoted into a bash script; a DSN with `&` (a second query parameter) splits the command (verified: exit 127). Fix: `Shellwords.escape` or argv-form. Status: open, low; waits on F-6.
- **F-43:** `release` without `-y` crashes with `NoMethodError` (`$stdin.gets.strip` on nil) when stdin is closed (gem_tasks.loki:54). asgard's copy already uses `.to_s`. Status: issue [#13](https://github.com/MadBomber/dev/issues/13).
- **F-44:** `quality`'s summary dies with `KeyError` at `STATUS_BADGES.fetch(status)` (quality.loki:458) when any swept-in `*_check` task returns a non-status value (e.g. a consumer's `def ruby_check = puts(...)`); the remaining rows and the fail line never print. Proposed: treat it as FAIL and name the task. Status: issue [#14](https://github.com/MadBomber/dev/issues/14) (best after #12 merges; asgard's quality.loki:218 has the same bug).
- **F-45:** an invalid `STATUS_DATE` (status_report.loki:27) ends `daily_summary` in a `Date::Error` backtrace after every collector has run; `Date.parse` also accepts words like "tuesday". Fix: `Date.iso8601` with a friendly abort. Status: PR'd ([dev#25](https://github.com/MadBomber/dev/pull/25), keeps `Date.parse`; strict ISO is a question for Dewayne).
- **F-46:** the ArchSpec SKIP message (quality.loki:426-428) tells a user who already has `archspec` on PATH to `gem install archspec`. Status: open, cosmetic (fold into the F-39 PR).
- **S-2 update:** wider than first recorded; details held locally. Status: low.
- **S-4:** security finding, details held locally. Status: open, medium-low.
- **S-5:** security finding, details held locally (extends S-1). Status: needs Dewayne's decision, low, latent.
- **S-6:** security finding, details held locally. Status: needs Dewayne's decision, low.

### 2026-11-13, sixth run (quality audit, second run; dead-code scan 5a0b89e..657e6bd; improver findings)

Scope: `develop` at 657e6bd, mainly the code dev#10–#12 changed. RuboCop Lint/Security has no new offenses (only F-17's). flay total 132, nothing new. In fixtures, reek_baseline → reek_check passes, then fails on a new file; with no lockfile, bundler_audit_check SKIPs; flog falls back to PATH; a Sunday STATUS_WEEK_OF snaps back to Monday. Dewayne's dev#11 comment (he'd rather use `.reek.yml` than a baseline, and may remove `.quality/`) is the context for F-47 and F-48. Dead-code scan: every method and constant #10–#12 added has a caller.

- **F-47:** `reek_check` and `reek_baseline` ignore `.reek.yml`'s `exclude_paths:` and `directories:`. `reek_smell_counts` (quality.loki:353-360) gives `Reek::Examiner` a String, not a path, and `quality_source_files` (:328-330) never filters excluded paths. Fixture: `reek lib` 5 warnings, `asgard reek_check` 20 and FAIL. Fix: `Reek::Source::SourceLocator` plus Pathnames. Status: issue [#23](https://github.com/MadBomber/dev/issues/23) (best after #19).
- **F-48:** `reek_check` (quality.loki:146-180) scores flog 63.1, above the repo's own fail line of 50; the report-building block would fit one testable helper. Status: needs Dewayne's decision (only worth it if the baseline and `.quality/` stay).
- **F-49:** the reek/flay "can not be loaded by asgard" messages (quality.loki:184, 446, 451) offer "outside the active bundle" as a cause, which only applies under `bundle exec asgard`; :184 repeats `skip_reek`'s text. Status: open, cosmetic (fold into the F-39/F-46 PR).
- **F-45 update:** `STATUS_WEEK_OF` (status_report.loki:301) has the same backtrace for `2026-13-01` and an empty value. Covered by dev#25.
- **F-50:** `quality_rails.loki` (:24/36/48) calls `bundled_gem?`, defined only in quality.loki:382; importing the Rails gates alone gives NoMethodError. Fix: `import "quality.loki"` at the top (asgard's import dedups). Status: issue [#24](https://github.com/MadBomber/dev/issues/24).
- **F-51:** `notes_collect` (status_report.loki:123) says "found today's journal entry" even when `STATUS_DATE` asks for another day. 1-line fix. Status: PR'd ([dev#48](https://github.com/MadBomber/dev/pull/48), 2026-11-15, with the matching `desc` strings and daily_summary's "_No activity captured today._"); file now `client/status_report.loki`.
- **F-52:** `status_report.loki:42` `safe_capture2` uses `Open3.capture2` without capturing stderr, so its git calls may leak `fatal:` lines like F-37 did. Status: issue [#28](https://github.com/MadBomber/dev/issues/28) (verified 2026-11-13; fix after #20, which edits that helper).

### 2026-11-13, seventh run (security audit, third run)

At 657e6bd; no new commits since the sixth run, so no dead-code scan (still at 657e6bd). Next free number: F-53.

- **F-52 update:** verified. git, gh and icalBuddy stderr reaches the terminal from `git_collect`, `github_collect` and `calendar_collect`; it never reaches the notes file. Filed as [#28](https://github.com/MadBomber/dev/issues/28).
- **F-36 update:** still holds after F-27. The SKIP lines now explain missing analyzers, but there's still no Ruby/asgard version, no "analyzer gems in asgard's Ruby" note, and no macOS flag. Filed as [#27](https://github.com/MadBomber/dev/issues/27).
- **S-7:** security finding, details held locally. Status: open, low.
- **S-8:** security finding, details held locally. Status: needs Dewayne's decision, low.

### 2026-11-13, eighth run (dependencies audit, second run)

At 657e6bd. No new commits, so no dead-code scan; it stays at 657e6bd. The new requires load on both Rubies, and every .loki loads without warnings. The analyzer gates run cleanly on Ruby 3.4/4.0 syntax, and every tool gem is at its latest release. F-27 is still fixed; F-32 to F-36 are unchanged. Next free number: F-54.

- **F-53:** `calendar_collect` says icalBuddy is missing whenever it fails for any reason. `safe_capture2` (status_report.loki:41-46) returns `["", false]` both when the binary is absent and on a non-zero exit, so :267 always prints "icalBuddy not available (brew install ical-buddy)". With icalBuddy 1.10.1 installed but unable to read calendars, it exits 1 with `error: No calendars.` (verified by the main session). Fix: tell ENOENT apart from a non-zero exit, and give the failure its own SKIP message. Best combined with F-52 (#28) after #20 merges. Status: issue [#29](https://github.com/MadBomber/dev/issues/29).

### 2026-11-14, ninth run (quality audit, third run; dead-code scan 657e6bd..6d0bb59)

At 6d0bb59. RuboCop Lint/Security: nothing new (F-17 now at quality.loki:521); flay total 132. Clean fixtures: `reek_check` with `.reek.yml` paths, `quality_rails.loki` loading alone and in either order, bad `STATUS_DATE` stopping `daily_summary` in one line. Dead-code scan reached 6d0bb59: nothing new. Next free number: F-57.

- **F-54:** `status_date_check` (status_report.loki:295, from dev#25) ends in `_check`, so `quality` (quality.loki:39) collects it in any repo importing both files; it returns a `Date`, counted as FAIL (`[FAIL] Status Date`). Fix: rename to e.g. `status_date_guard` and update :298. Status: issue [#35](https://github.com/MadBomber/dev/issues/35); PR after Dewayne's #32 merges (same file).
- **F-55:** `build`/`install` (gem_tasks.loki:46, :53) use `gem_version`'s `"?.?.?"` fallback in the file name; `release` aborts only after `quality` and `build`. Fixture `VERSION = [0, 1, 0].join(".")` built `pkg/foo-?.?.?.gem`. Status: issue [#36](https://github.com/MadBomber/dev/issues/36), low. Dewayne's direct commit 664614c on `main` also touches `release`.
- **F-56:** `flog_check` compares with `>` (quality.loki:100, :102) though its desc (:65) and report header (:112) say `>=`. Split out of F-39. Status: issue [#37](https://github.com/MadBomber/dev/issues/37).
- **F-16 update:** `branch_report` flog 104.7 → 111.9 after dev#22; flay mass 54 at :152/:157 unchanged. Still open, low.
- **F-5 update:** `branch_report.loki` part PR'd ([#38](https://github.com/MadBomber/dev/pull/38)); `db_doc.loki`, `.irbrc` wait on F-6.
- **F-39/F-46/F-49 update:** PR'd ([#39](https://github.com/MadBomber/dev/pull/39)); :187 wording and the "Tests + Coverage" label left for Dewayne.

### 2026-11-15, tenth run (security audit, fourth run; dead-code scan 6d0bb59..8113aab)

Audited `develop` at 8113aab (== `main`): `aws.loki`, `fake_aws.loki`, `fake_aws/` (Dockerfile, smoke.rb), `rails/db.loki`, `rails/dev.loki`, `runbook.loki`, `client/ticket.loki`, and the `client/status_report.loki` and `gem_tasks.loki` changes. No eval/Marshal; YAML only via `safe_load_file`; the new aws/ticket/db/runbook tasks pass argv, not shell strings. No lockfile, so no `bundle audit`. Dead-code scan: nothing new. Next free number: F-62.

- **F-57:** `fake_aws` builds shell text from `Dir.home` without quoting (`export_lines` :146-158, `docker_run_command` :121-133, `healthy?` :136-140); a HOME with a space breaks `eval "$(asgard fake_aws exports)"` (verified with a fixture HOME). Status: issue [#50](https://github.com/MadBomber/dev/issues/50), low.
- **F-58:** `Integer(ENV.fetch("AWS_EMULATOR_PORT", "9297"))` (fake_aws.loki:25) runs at import, so `AWS_EMULATOR_PORT=abc` breaks every asgard command in a repo importing `dev/.loki` (verified by the main session). Status: issue [#49](https://github.com/MadBomber/dev/issues/49), low.
- **F-59:** `dev down` (rails/dev.loki:99-110) trusts pid files; a reused pid gets a group TERM, and `pgrep -x mailpit` matches other users' mailpit. Status: unverified (by reading), low; needs a choice of how to identify the process.
- **F-60 (improver):** `fake_aws/smoke.rb` `check` rescues and prints FAIL but the script always exits 0. Status: issue [#51](https://github.com/MadBomber/dev/issues/51), low.
- **F-61 (builder, dev#53):** `rails/quality_rails.loki:28` imports `"../quality.loki"` by relative path; through a symlinked `dev/` with rails/ imported before quality, quality loads twice (`already initialized constant`). Fix: #53's `__FILE__` + `Asgard.glob_escape` form. Status: open, low (after #53).
- **GateGuard (improver):** `quality.loki` `GateGuard#invoke_command`'s method-level rescue (from 65a9c31) also covered non-gate tasks and Thor's unknown-command error, leaving `<task>_output.txt`. Status: PR'd ([dev#52](https://github.com/MadBomber/dev/pull/52)).
- **F-50 update:** fixed by Dewayne (`rails/quality_rails.loki:28` imports quality.loki).
- **F-5 update:** the `db_doc.loki` header was rewritten by Dewayne; `.irbrc:1` still names another project's path (waits on F-6). `rails/db_doc.loki:15` still defaults to `vbr_development`.
- **S-9:** security finding, details held locally. Status: open, low.
- **S-10:** security finding, details held locally. Status: needs Dewayne's decision, medium-low.
- **S-11:** security finding, details held locally. Status: open, low.
- **S-12:** security finding, details held locally. Status: needs Dewayne's decision, low.
- **S-13:** security finding, details held locally. Status: unverified, low.

### 2026-11-15, eleventh run (dependencies audit, third run; triage and improver findings)

Audited `develop` at 8113aab (unchanged since the tenth run). Fixtures with asgard 0.6.0 on Ruby 4.0.9 and 4.1.0-preview1, plain and under a Gemfile holding only `gem "asgard"`. Next free number: F-71.

- **F-62:** `client/ticket.loki:101` requires `base64` but never uses it; under `bundle exec asgard` without base64 in the Gemfile, every command fails with `cannot load such file -- base64 (LoadError)` (verified by the main session; same class as F-24). Fix: delete the line. Status: issue [#54](https://github.com/MadBomber/dev/issues/54).
- **F-63:** `fake_aws/smoke.rb` needs aws-sdk-s3 and aws-sdk-sqs, which no doc names; without them it dies with a LoadError backtrace. It also uses `Net::HTTP` without `require "net/http"`. Fix: rescue `LoadError` with an install hint, add the require, and name the gems in README/llms.txt. Status: open, low; ship with the #51 fix (same file).
- **F-64:** the `rails/db.loki:68` hint `brew install postgresql` installs keg-only `postgresql@18`, so the tools still aren't on PATH. Fix: add the `$(brew --prefix postgresql@18)/bin` PATH step. Status: open, low.
- **F-65 (triage):** `live_pid_from` (`rails/dev.loki:37-48`) turns an empty pid file into pid 0, and `Process.kill(0, 0)` succeeds, so `dev status` says `running (pid 0)` (verified by the main session); `dev up` would refuse to start and `dev down` would TERM `-0`. Fix: treat a non-positive pid as stale. Status: issue [#55](https://github.com/MadBomber/dev/issues/55).
- **F-66 (triage):** `jira_markdown_table` (`client/ticket.loki:415-419`) does not escape `|`, so a ticket title with a pipe adds a column (verified by the main session; same as F-13). Status: issue [#56](https://github.com/MadBomber/dev/issues/56).
- **F-67 (improver):** `client/ticket.loki:108` loads `tty-table` at import, though only `jira_terminal_table` uses it. asgard itself depends on tty-table, so this only bites an asgard install without it (shown with a stub). Status: open, low; probably not worth a PR unless asgard drops the dependency.
- **F-68 (improver):** `rails/dev.loki:62-63` accepts `DEV_SERVER_COMMAND` only when its first word is a file path, so `foreman start …` or `ruby …` on PATH aborts with "not found — run this from the Rails app's root" (verified by the improver). Fix: also accept a command `which` finds. Status: open, low.
- **F-69 (improver):** stale comments: `fake_aws.loki:7` shows `import "fake_aws.loki"` (consumers use `import_up "dev/fake_aws.loki"`), and `fake_aws/Dockerfile:3` says `asgard build` sets the arch (it's `asgard fake_aws build`). Status: open, cosmetic.
- **F-70 (improver):** `ticket sync` defaults to `~/scripts/jira_get.rb` and `~/scripts/md2html` (`client/ticket.loki:1018`, :1020), which aren't in this repo. Status: needs Dewayne's decision (inbox).
- **S-12 update:** the auditor rechecked `fake_aws/Dockerfile:6-7` independently and reached the same finding; held locally.
