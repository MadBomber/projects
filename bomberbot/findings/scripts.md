# Findings: scripts

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | Dependabot alert review plus a grep for removed Ruby APIs; no Gemfile at repo root, so no bundle-audit. |
| dead code | 2026-11-07 (quick scan only) | Quick scans of 73d6c82..968704b, 968704b..af11b69, af11b69..5fbd330 and 5fbd330..543c66a (only #13 to #16, all small fixes); nothing removable. No full dead-code audit yet — still the most-overdue item next visit. The 2026-11-09 quality audit confirmed F-17/F-18 as the strongest removal candidates alongside standing F-10. |
| dependencies | 2026-11-06 (partial) | `ruby -wc` sweep of all 225 tracked Ruby files, plus a grep for APIs removed in Ruby 3.x and 4.x. |
| adoption | n/a | Skipped for this repo by rule. |
| security | 2026-11-10 | First full read at develop bc51825: every eval/send/constantize, YAML/Marshal load, interpolated shell-out and secret ENV read in top-level scripts + `lib/` + `templates/` (archives excluded). bundle-audit n/a (no root Gemfile/lockfile). S-2 (reproduced), S-3, S-4; details held locally. |
| quality | 2026-11-09 | FIRST full run. reek 6.6.0 over all top-level `*.rb` + `lib/` at develop 543c66a; ~99% style smells triaged out. Net-new: F-17 (find_unused_methods.rb broken), F-18 (seed_with_headers.rb missing requires), F-19 (79 files: 3+ consecutive puts vs heredoc). Removed-API sweep (File.exists?, Fixnum, URI.escape, .untaint, ERB safe-level) clean. |
| dead code | 2026-11-09 (**first full pass**) | Whole-tree pass at develop 543c66a (171 top-level `*.rb` + `lib/`). Suite is `ruby -c` only (no `test/`, no CI, no root Rakefile; nothing enumerates script names). Re-verified F-10/F-17/F-18 — each present, referenced only by itself + one README row, `ruby -c`-green to remove. New F-23 (`estimate_tokens.rb`, inert never-called snippet). No other new dead code. Archive dirs (`_old_stuff_and_1offs`, `retirement_home`) are the owner's deliberate graveyard, not targets. |
| dependencies | 2026-11-10 (**first full pass**) | Full pass at develop bc51825: `ruby -c`/`-wc` across 208 tracked Ruby files (archives excluded; only `templates/class.rb` placeholder "fails"), grep sweep for Ruby 3.x/4.x removed/deprecated APIs (all clean — #14/#15 hold), 114-name require-loadability probe (uninstalled personal gems expected, not relics), and Psych-4 `YAML.load` readiness. No root Gemfile → bundle-audit n/a. Ruby 4.1.0preview1 runs everything except the uncovered `YAML.load` sites. Filed F-31..F-33. |
| quality | 2026-11-10 | Runtime-defect pass at develop bc51825, not style. All 171 top-level `*.rb` run three ways (no args, `-h`, missing-file arg) under a `sandbox-exec` profile (no network, writes only to scratch/tmp, no signals). Every crash was read by hand and seven fixes were confirmed on a scratch copy. New F-38..F-48. **Two scripts are destructive on `-h` (F-44 normalize_pathnames, F-45 uninstall_all_gems): never run them unsandboxed.** The dead-code quick scan 543c66a..bc51825 found nothing removable. Dead-code scan last covered commit: bc51825. |
| dead code | 2026-11-12 | Quick scan bc51825..86201ce (#24, #25, #26): nothing removable; #26 left no orphaned code. F-10/F-17/F-18/F-23 re-checked: still there, each referenced only by itself and one README row (now L249/L119/L251/L109). Sweep of `lib/` (all file types) and of top-level methods referenced only once: new F-52 (unused pyvis JS/CSS assets in `lib/`, ~740 KB) and F-53 (`-v` flag that does nothing in csv_reader/docx_style_fixer/new.rb, plus unused `csv2json` `header?`). Archive dirs excluded on purpose. |
| security | 2026-11-12 (focused) | Second pass at develop 86201ce: ~110 non-`.rb`/extensionless scripts (sh/bash/perl/python/extensionless Ruby, sql/, config/, database_dumps/, committed .envrc), diff bc51825..86201ce (clean), first full-history secret scan (gitleaks, 541 commits: one false positive in minified vis-network.min.js). bundle-audit n/a. S-6..S-8 held locally; F-60..F-64 public. Dead-code scan last covered commit: 86201ce (develop unchanged). |
| dependencies | 2026-11-12 | Ruby 4.0/4.1 readiness pass at develop 86201ce: bundled/default-gem moves across 3.4.6, 4.0.9, 4.1.0-preview1; load probe of every bundled or removed library; `ruby -wc` on 4.0.9 and 3.4.6; sandboxed `-W:deprecated` runs (no args, `-h`) on 4.0.9; Bundler/`bundler/inline` scripts; template Gemfiles. No root Gemfile, so bundle outdated/audit n/a. New F-70..F-76; F-45 and F-66 extended. Dead-code scan last covered commit: 86201ce (develop unchanged). |
| quality | 2026-11-12 | Logic-defect pass at develop 86201ce over ~95 scripts no earlier finding names (top-level `.rb`, extensionless Ruby, shell, `lib/`, `templates/`); archives excluded. Every candidate reproduced under `sandbox-exec` (scratch copies, stub brew/Octokit, in-process Rack, throwaway git repos). New F-80..F-97 (improver F-98, F-99; S-9 held locally). **F-80 (`move_fork_offline.rb`) is data loss.** Dead-code scan last covered commit: 86201ce (develop unchanged). |
| dead code | 2026-11-13 | Quick scan 86201ce..a35981f (#50-#67 plus the #35-#37 merges, 38 files): nothing orphaned, no new auditor findings. F-10/F-17/F-18/F-23/F-52/F-79/F-99 still present and referenced only by their README rows. erb2haml is now the last non-archive systemu user (F-68). Dead-code scan last covered commit: a35981f. |

Dead-code scan last covered commit: 86201ce (develop, 2026-11-12; quick scan bc51825..86201ce, nothing removable)

## Findings

### 2026-11-06

- S-1: security finding, details held locally. Status: merged ([#13](https://github.com/MadBomber/scripts/pull/13), 2026-11-07; on develop, not yet on main).
- F-1: `templates/sinatra_app` shipped a stale Gemfile.lock and a `ruby "2.3.1"` pin, plus the unused refile gem that holds sinatra at 1.4. As a result, generated apps failed `bundle install`. Status: merged ([#13](https://github.com/MadBomber/scripts/pull/13), 2026-11-07).
- F-2: `Fixnum` (removed in Ruby 3.2) is still used in `get_next_parameter` in `docx_to_txt.rb:63`, `rultor_init_project.rb:91` and `s3_uploader.rb:82`. This finishes Dewayne's own 2e3de64, which fixed clone_all.rb and release.rb. The rultor_init_project and s3_uploader call sites can be reached through `-o`/`-r`/`-u`. The docx_to_txt call site is dead (`get_out_filename` is never called). Status: merged ([#14](https://github.com/MadBomber/scripts/pull/14), 2026-11-07); used `Integer == param_index.class` to match 2e3de64. Best next PR: swap to `param_index.is_a?(Integer)`. Note that the docx scripts need a local `docx_helpers` lib that isn't in the repo, so they can't be seen running here.
- F-3: `docx_style_lister.rb:106` uses `File.exists?`, which was removed in Ruby 3.2, and raises NoMethodError on every run that has arguments. Status: merged ([#14](https://github.com/MadBomber/scripts/pull/14), 2026-11-07).
- F-4: `imbibler.rb:29,113` uses `URI.escape` and the open-uri `Kernel#open(url)`, both removed in Ruby 3.0. Status: open. The fix needs `URI.open` plus an escape replacement (`URI::RFC2396_PARSER.escape`), and the script scrapes a website, so seeing it run means a network GET.
- F-5: `YAML.load` on `gem spec` output in `describe_gems.rb:50`, `squish_gem_list.rb:89` and `useful_gems.rb:494`. Under Psych 4 this likely raises on the `!ruby/object:Gem::Specification` tags. Status: PR'd ([#21](https://github.com/MadBomber/scripts/pull/21), 2026-11-09). Verified/reproduced on Ruby 4.1.0preview1/Psych 5 this visit (`YAML.load(\`gem spec json\`)` → `Psych::DisallowedClass`); fix swaps the three sites to `YAML.unsafe_load` (trusted local `gem spec` output, not a security issue). describe_gems/squish_gem_list were unrescued (crash); useful_gems was silently failing inside a bare rescue.
- F-6: `templates/sinatra_app/app.rb:15` has `raise APP_BIND, ...`, which raises NameError. It should be `raise MissingSystemEnvironmentVariable`. Status: open, verified by the #13 code review.
- F-7: `templates/sinatra_app/Gemfile` has no `rake` gem, so `bundle exec rake` in a generated app fails. Status: open.
- F-8: `templates/sinatra_app` uses thin, whose eventmachine dependency doesn't compile on Ruby 4.1.0-preview1, and the template also uses the end-of-life `sass` gem. Status: needs Dewayne's decision (switch `:server` to puma and drop thin; drop sass or move to sass-embedded; or wait for upstream).
- F-9: `new.rb:152` copies directory templates with an unquoted `system "cp -R #{...}"`, which breaks when the cwd contains spaces. `FileUtils.cp_r` would fix it. Status: PR'd ([#16](https://github.com/MadBomber/scripts/pull/16)).
- F-10: `s3_uploader.rb` is a self-described "TODO: complete this stub". It could be removed, but nobody has checked whether anything uses it. Status: unverified.

### 2026-11-07

No audit ran: the backlog still had 3 or more verified open findings (F-4, F-6, F-7, F-9), and the 2026-11-06 audits are 1 day old. A quick dead-code scan of 73d6c82..968704b found nothing removable.

- F-4 re-confirmed: `imbibler.rb:29,113` still calls `URI.escape` and the open-uri `Kernel#open(url)`. The #14 code review flagged it again. Status: open, verified. Best next Ruby-compat PR.
- F-10 note: #14 got `s3_uploader.rb` past option parsing. It then stops at `hipchat_room_id`, which is undefined, and it requires `hipchat_s3`, which isn't installed. HipChat itself was shut down in 2019. This adds weight to the removal case, though a removal still needs evidence that nothing uses it. Status: unverified.
- F-11: `get_next_parameter` is copied into 7 scripts (clone_all, release, rultor_init_project, s3_uploader, docx_to_txt, docx_rm_blank_paragraphs, csv_reader). In the copies that take a String, `ARGV.find_index` can return nil and crash on `nil + 1`, but every current caller checks `ARGV.include?` first, so nothing triggers it today. Status: open (low; consolidating the copies into `lib/` is a design choice, not an obvious-yes PR).

### 2026-11-07, second run

No audit ran: the backlog still had 3 or more verified open findings (F-4, F-9, plus F-6 and F-7, which are held), and the audits are 1 day old. A quick dead-code scan of 968704b..af11b69 (#14's four one-line fixes) found nothing removable.

- F-4: Status: PR'd ([#15](https://github.com/MadBomber/scripts/pull/15)). The fix also swaps `Pathname#realpath` for `expand_path` on OUTPUT, since `realpath` raised ENOENT when the CSV didn't exist yet. It also extracts `fetch_page` and guards main so the script can be loaded, and corrects the README description.
- F-12: The README described `imbibler.rb` as a "Drinking game or beverage tracker". Status: PR'd ([#15](https://github.com/MadBomber/scripts/pull/15)). Other one-line README descriptions may be guesses from the file name too. Status of that wider question: unverified.
- F-13: Problems in `imbibler.rb` that the #15 reviews raised and left alone, all already in the code:
  - `RFC2396_PARSER.escape`, like the old `URI.escape`, double-encodes `%` in hrefs that are already escaped.
  - A missing `p span.text`, an untitled link or an HTTP error crashes the whole run with no nil guard or rescue.
  - The lazy `(.+?)` at the end of the verse regex keeps only one digit.
  - `format('%03d', '08')` parses as octal and raises.
  - `bookNum.succ!` mutates a string literal, which warns under `-W:deprecated` on 4.x.
  - Chapter URLs are built from a hard-coded `http://www.biblegateway.com` prefix instead of `URI.join`.
  - The usage text says the cwd, but files go next to the CSV.
  - `CSV.open` is used without a block form.
  - There are no fetch timeouts.

  Whether the site's current markup still matches is unknown. Status: open (low; each is small, but none is worth a PR until someone actually wants to run the scraper again).
- Correction: Dependabot PRs #10 to #12 (opened 2021 to 2023) were closed on 2026-11-07, during the second run. GitHub reports 30 open Dependabot alerts on the default branch (`main`); they most likely come from the old `templates/sinatra_app/Gemfile.lock` that #13 removed on develop, and should clear when develop is merged into main. Status: unverified until that merge.

### 2026-11-07, third run

No audit ran. Verified open findings that aren't held were F-9 plus the new F-15; F-6 and F-7 are held behind F-8, and the audits are 1 day old. A quick dead-code scan of af11b69..5fbd330 (#15's imbibler fix) found nothing removable.

- F-9: Status: PR'd ([#16](https://github.com/MadBomber/scripts/pull/16)). Reproduced on develop: from a cwd with a space, `cp` prints `Not a directory` and the script exits 0.
- F-14: Other scripts also build shell commands with unquoted paths: `create_working_directory.rb:121`, `copy_missing_files.rb:121`, `schema_diff.rb:61,80,81,101` and `change_git_repos_to_ssh_access.rb:25,34`. `my_docx_style_fixer.rb:107-124` single-quotes its paths, which still breaks on a `'`. Found by the #16 altitude review. Status: unverified (each needs its own reproduction).
- F-15: In `new.rb:112-123`, the existence check runs before the `.erb` fallback. A template that exists only as `<name>.erb` is always rejected, so `new Gemfile` (listed in the usage) exits 1 with "no such template file: Gemfile". Status: PR'd ([#17](https://github.com/MadBomber/scripts/pull/17), 2026-11-09). Reproduced on develop (exit 1) and after the fix (renders `Gemfile.erb`, exit 0); fix resolves the `.erb` version before the existence check, keeping the user's typed name in the error.
- F-16: Other older `new.rb` issues that the #16 code review raised:
  - `-v` does nothing: `debug_me` always runs and `verbose?` is never used.
  - The `-v`/`--verbose` loop clears only the first copy of each flag.
  - `STDIN.gets.chomp` crashes when STDIN is at EOF.
  - `gsub!` with a String replacement treats `\0` and `\&` in user input as back-references.
  - `PH_REGEX` merges or misses placeholders that touch other characters.
  - Directory templates get no placeholder filling.
  - `templates_path + f` accepts absolute paths and `..`.

  Status: open (low; unverified apart from the code read).

### 2026-11-09, fourth run

Quality audit (first full run) plus the improver's README spot-check. New findings F-17 to F-22. Verified open findings this visit were F-15 (shipped #17) and the README defects (shipped #18 / filed #19); the audits are current.

- F-17: `find_unused_methods.rb:43-63` cannot run in any path — three scope/ordering bugs: `process_a_file` is called (L47) before its `def` (L51) so Ruby raises `NoMethodError`; inside the method `a_child`/`method_name_regex` are outer-scope locals a `def` can't see (`NameError`); and `method_defs` (L61/63) should be the global `$method_defs` (L41). Also shells out to `ag` (non-stdlib) and carries typo comments + an `__END__` scratch block — an abandoned experiment. Status: needs Dewayne's decision (fix vs retire; inbox). Also the strongest dead-code/removal candidate found this visit.
- F-18: `seed_with_headers.rb:62,74` `require_relative 'utilities'` and `'models/localhost_all'` — neither file exists anywhere in the tree, so it raises `LoadError` once reached, on top of many non-stdlib deps (active_record, pg, cli_helper, pretty_backtrace). Rails-era relic; violates the stdlib-only rule. Status: needs Dewayne's decision (fix vs retire; inbox).
- F-19: 79 tracked `*.rb` files (archives excluded) have a run of 3+ consecutive static `puts`, almost all usage/help banners — the exact case the "3+ consecutive puts become one heredoc" taste rule targets (e.g. `bible_semantic_search.rb:30-35`, `csv2json.rb:40-44`, `aip.rb:358-361`). A batch conversion is wholesale (against the no-wholesale-restructuring rule); each file is independently a small provable PR if Dewayne wants them converted incrementally. Status: needs Dewayne's decision (inbox).
- F-20: README script-index rows with WRONG descriptions (guessed from the filename; the script does something else): `bp.rb` ("Boilerplate code generator" → extracts blood-pressure data from email), `ct.rb` ("Code testing utility" → colortail: tail+colorize a log), `disable.rb` ("Disable features or services" → enable/disable Rails migrations by timestamp), `dit.rb` ("Directory iteration tool" → diff old.xml vs new.xml), `docx_brf.rb` ("Convert DOCX to BRF (Braille)" → find bible references; BRF = Bible ReFerences), `organizer.rb` ("Organize files into directories" → process a text file of addresses). Each corrected from the script's own `## Desc:` header/code. Status: PR'd ([#18](https://github.com/MadBomber/scripts/pull/18), 2026-11-09).
- F-21: README lists four rows for files deleted from the repo — `brewt_all.md`, `create_data_dictionary`, `data_dictionary.rb`, `gems_v344.txt` (all absent from `git ls-files`). Status: PR'd ([#18](https://github.com/MadBomber/scripts/pull/18), 2026-11-09).
- F-22: README script index is missing rows for 22 tracked top-level `.rb` scripts (e.g. `churn.rb`, `branch_report.rb`, `mp4_to_m4a.rb`, `llmstxt_generator.rb`, `jira_get.rb`, `pr_get.rb`, `tdv.rb`, `remadd.rb`). Repro: `for f in $(git ls-files '*.rb' | grep -v '/'); do grep -qE "^\| *$f " README.md || echo "$f"; done | sort` prints 22 names on develop 543c66a. Status: issue [#19](https://github.com/MadBomber/scripts/issues/19). A companion `rake`/CI drift check (flag README rows with no file, and scripts with no row) would stop F-20/F-21/F-22 recurring — inbox.

### 2026-11-09, fifth run

First full whole-tree **dead-code** audit (171 top-level `*.rb` + `lib/` at develop 543c66a) plus triage/improver leads. Verified open findings shipped this visit: F-5 (#21), F-24 (#22), F-25 (#20); #22 also resolves #19. F-14 became issue #23. New F-23. Suite is `ruby -c` only (no `test/`, no CI, no Rakefile).

- F-5: Psych-4 `YAML.load` crash in three gem-spec scripts. Status: PR'd ([#21](https://github.com/MadBomber/scripts/pull/21)) — see updated entry under 2026-11-06.
- F-10 (re-verified, full evidence): `s3_uploader.rb` is a self-described `# TODO: complete this stub` for HipChat (shut down 2019); `require 'hipchat_s3'` (non-stdlib), body is ASCII-art + verbatim hipchat_s3 README example referencing undefined `hipchat_room_id`; no S3 implementation. Referenced only by itself + README row 233 (`git grep s3_uploader|hipchat`). `ruby -c` green to remove. The auditor calls it the strongest obvious-yes removal, but inbox already asks Dewayne fix-vs-retire (entry added 2026-11-09), so not shipped unasked. Status: needs Dewayne's decision (removal candidate, fully evidenced).
- F-17 (re-verified): `find_unused_methods.rb` un-runnable (3 scope/order bugs + non-stdlib `ag`/`amazing_print` + `__END__` scratch). Referenced only by itself + README row 117. Status: needs Dewayne's decision (fix vs retire).
- F-18 (re-verified): `seed_with_headers.rb` `require_relative 'utilities'`/`'models/localhost_all'` — neither exists; also active_record/pg/cli_helper/pretty_backtrace. Referenced only by itself + README row 235. Status: needs Dewayne's decision (fix vs retire).
- F-23 (new, dead code): `estimate_tokens.rb` (12 lines) is header `# TODO: complete this idea` + one `def estimate_tokens` and nothing else — no requires, no main, no caller anywhere (`git grep estimate_tokens` → only README row 107 + the def). Runs without error but does nothing. Unlike F-10/F-17/F-18 it isn't broken — a working snippet Dewayne may keep as a copy-paste library. Status: needs Dewayne's decision (fix vs retire; inbox).
- F-14: unquoted `cp -R` shell paths break on spaces (same class as merged F-9/#16): verified `create_working_directory.rb:121` and `copy_missing_files.rb:121` (repro: `sh -c "cp -R $SOURCE_DIR $TARGET_DIR"` with a space → `cp: dir: Not a directory`). Other candidate sites (schema_diff, change_git_repos_to_ssh_access, my_docx_style_fixer single-quoting) not individually reproduced. Status: issue [#23](https://github.com/MadBomber/scripts/issues/23).
- F-24 (new, docs): three more README script-index rows with WRONG descriptions (same class as F-20): `shc.rb` ("Shell script compiler" → Section Header Comment generator, wraps text in a `#`-banner), `picnic_gems.rb` ("Collection of fun Ruby gems" → install gems from a text file, migrate between Ruby versions), `rawk.rb` ("Ruby AWK-like text processor" → "Rail's Analyzer With Klass", a Rails log timing analyzer). Each corrected from the script's own header/code. Status: PR'd ([#22](https://github.com/MadBomber/scripts/pull/22)).
- F-25 (new, defect): `prepend_to_file.rb:9,58` print the usage/banner with the wrong program name `prepend_content.rb` (a file that doesn't exist); a user copying the usage line runs a bad command. Repro: `ruby prepend_to_file.rb` → `Usage: prepend_content.rb ...`. Fixed with `File.basename($0)`. Status: PR'd ([#20](https://github.com/MadBomber/scripts/pull/20)).
- Note (improver): `acronym_extractor.rb:122-128` has a dead identical `if/else` (both branches `word[0].upcase`) collapsible to `map { |w| w[0].upcase }` — behaviour-preserving simplification, no runtime harness (needs rwordnet/ffi-hunspell), not shipped this visit. Lower-confidence README rows `buzz_words.rb` and `fake.rb` left alone (descriptions arguably defensible).
- Note: the #19 missing-row detector `^\| *$f ` false-flags any row whose name fills the column with no trailing pad space; `create_erd_scripts_from_rails_models.rb` already had a row and was mis-flagged — #22 fixed its padding rather than duplicating it. So the true count was 21 genuinely missing + 1 padding quirk. Detector hardening noted in inbox.

### 2026-11-10

Security audit (first full read of shell-outs / eval / deserialization / secret env vars) at develop bc51825. bundle-audit not applicable (no root Gemfile; sinatra_app lockfile removed by #13). Quick dead-code scan of 543c66a..bc51825 (merges of #17, #18, #20, #21): nothing removable. Dependabot alerts API now returns 403 "disabled for this repository".

- S-2: security finding, details held locally. Status: open (verified by reproduction); small fix queued as the first backlog item for next visit.
- S-3: security finding, details held locally. Status: open (low).
- S-4: security finding, details held locally. Status: open (low).
- Reviewed and cleared (not findings): `comment_gemfile.rb:79` eval of the user's own Gemfile lines; `cost_of_trip.rb:59` evals local variable names only; `fake.rb:15-16` `send` guarded by `respond_to?`; `rss_tracker.rb:141`/`rawk.rb:82`/`bp.rb:152` `send` with fixed or CLI keys; `YAML.load` in `yaml2json.rb`, `vcr_hater_yaml2hash.rb`, `convert_vcr_response_to_fixture.rb`, `run_sql.rb` is safe_load by default under Psych 4+; F-5's `unsafe_load` sites read trusted local `gem spec` output.
- Status updates: F-5 merged (#21); F-15 merged (#17); F-20, F-21 merged (#18); F-25 merged (#20). F-24 + #19: #22 merged into the stale stack branch, not develop; re-landed as PR'd ([#25](https://github.com/MadBomber/scripts/pull/25)).
- F-6: Status: PR'd ([#24](https://github.com/MadBomber/scripts/pull/24), 2026-11-10). Not actually blocked by F-8: the guard runs before any gem `require`, so plain `ruby app.rb` shows NameError → MissingSystemEnvironmentVariable. F-7 stays held behind F-8.
- F-14: copy steps in `create_working_directory.rb` and `copy_missing_files.rb` (plus the single-file `cp` in copy_missing_files) PR'd ([#26](https://github.com/MadBomber/scripts/pull/26), Fixes #23). Remaining sites still open: `schema_diff.rb`, `change_git_repos_to_ssh_access.rb`, `my_docx_style_fixer.rb`, and (auditor) `gc.rb:85`, `image_metadata.rb:34`, `disable.rb:87`, `convert_html_to_text.rb:28`, `monitor_websites.rb:54` — none reproduced individually.
- F-26 (new, defect): five usage lines name non-existent programs (same class as F-25): `bp.rb:54`, `convert_position_to_named_parameters.rb:12`, `docx_draft_layout.rb:34`, `docx_extract_bodytext.rb:19`, `github_batch_delete_repos.rb:9`. Verified by running each with no args. Status: issue [#27](https://github.com/MadBomber/scripts/issues/27).
- F-27 (new, defect): `create_working_directory.rb:108,133,138,147,154` run `cd #{path} && ...` unquoted and ignore failure, so on a path with a space the branch checkout/direnv/prep steps are skipped and it still prints "Done." Seen by the #26 builder. Status: issue [#28](https://github.com/MadBomber/scripts/issues/28).
- F-28 (new, low): `create_working_directory.rb` never creates the parent dir for branch names like `feature/T1` (its comment says it may need to); with #26 the copy now raises there. Also re-running for the same branch nests a copy inside the existing dir (pre-existing). Status: open (unverified beyond the builder's run).
- F-29 (new, low, template): `templates/sinatra_app` env guards accept empty strings; `Rakefile:9` loads `app.rb` so every rake task needs APP_BIND/APP_PORT; `config/puma.rb` ignores them and uses `puts`; template README setup says "TBD". Status: open (held with F-7/F-8 template work).
- F-30 (new, docs): README index still lacks rows for 18 tracked non-`.rb` top-level entries (2pdf, cfcsv2mintcsv, create_image_of, epic_graph, gems_v405..v4101.srt, gemt2, gemt2_all.md, install_nng.sh, jira2md, rand, uuid, uuid_v4, uuid_v7); a few rows are out of sort order. Status: open (good docs PR after #25 merges).

### 2026-11-10, second run

Tend-and-record only: **skipped new PR work — review queue full** (3 open bomberbot PRs #24/#25/#26, none approved, none low-risk self-mergeable). Dependencies audit (first full pass, F-31..F-33) plus triage/improver leads (F-34..F-37). Responder linked the fixing PRs on #19 and #23. Three issues filed (#29/#30/#31). Quick dead-code scan not re-run (no new develop commits since bc51825). S-2 backlog item stays queued (not shipped — queue full).

- F-31 (new, dependency/Ruby-4, verified): `yaml2json.rb:41` `YAML.load file_contents` raises `Psych::AliasesNotEnabled` under Psych 4+ (bundled Ruby 3.1+; 5.5 here) on any YAML with anchors/merge keys (`<<: *default`) — the Rails `database.yml` / VCR idiom this converter targets. Repro: `printf 'defaults: &d\n  a: 1\ndev:\n  <<: *d\n' > s.yml; ruby yaml2json.rb s.yml` → `Psych::AliasesNotEnabled`. Fix: `YAML.load(file_contents, aliases: true)` (keeps safe-load; **not** `unsafe_load` — reads an arbitrary CLI-named file). Status: issue [#30](https://github.com/MadBomber/scripts/issues/30).
- F-32 (new, dependency, verified mechanism): `run_sql.rb:88` and `seed_with_headers.rb:68` feed a Rails `database.yml` (ERB-rendered) into `YAML.load`; same `Psych::AliasesNotEnabled` on the near-universal `<<: *default`. Both scripts are also gated by uninstalled activerecord/pg/configatron, so not run end-to-end. These are trusted-local config files, so `unsafe_load` *or* `aliases: true` both fit. Status: open (verified mechanism). Fold into the #30 fix.
- F-33 (new, dependency, unverified trigger): `convert_vcr_response_to_fixture.rb:54` and `vcr_hater_yaml2hash.rb:47` `YAML.load` cassette files; cassettes with aliases/anchors would raise the same way, but no in-repo sample proves it. Cassettes may be third-party → prefer `aliases: true` over `unsafe_load`. Status: unverified.
- F-34 (new, defect, verified): `docx_translate.rb:80,96` append to the undefined global `$errors` (author's own `# FIXME: errors is not global`) while the script defines and prints a **local** `errors = []` (L57, L111-124). Giving `-f`/`--from`/`-t`/`--to` with no value raises `NoMethodError: undefined method '<<' for nil` instead of the intended "specified without parameter" message. Reproduced in a stdlib harness of L57-96. Fix: drop the `$` sigil on both lines. It is the lone sibling of the `$errors = []` idiom (clone_all/rultor_init_project/s3_uploader/my_docx_style_fixer) that made the collector local but still appends to the global. Status: issue [#31](https://github.com/MadBomber/scripts/issues/31).
- F-35 (new, defect/docs-vs-code, verified): `mbox_finder.rb:71` `a_downcased_line = a_line` never downcases the line, so the case-insensitive keyword search the usage promises (L26-28) and the lowercased `$key_words` (L60) expect silently misses any mismatched-case match. Repro: a `.mbox` with `Subject: My Backpack trip` searched for `backpack` reports nothing; with `a_line.downcase` the message is found (confirmed before/after). Fix: `a_downcased_line = a_line.downcase` (one line, matches the variable name). Status: issue [#29](https://github.com/MadBomber/scripts/issues/29).
- F-36 (new, defect, verified): `insert_ticket_into_changelog.rb:57-61` sets `JIRA_TICKET` from the first positional arg then calls `JIRA_TICKET.empty?`; with no positional arg (e.g. only `--dryrun`) `JIRA_TICKET` is nil and `nil.empty?` raises `NoMethodError` instead of printing the intended "No JIRA Ticket supplied" error. Repro: `RR=/tmp ruby insert_ticket_into_changelog.rb --dryrun`. Fix: `JIRA_TICKET.to_s.empty?`. (This script is also security S-3 — JIRA creds on the curl argv — held locally; the crash itself is not security.) Also a usage typo `[pptions]`→`[options]` at L26. Status: open (verified; not filed, 3-issue cap reached this visit).
- F-37 (new, docs-vs-code, verified): `image_renamer.rb:29` `cli_helper("Resize image(s) to a standard size/geometry")` — copy-paste from `image_resizer.rb`; the script renames (`img_NNNN.ext`), so `--help` prints the wrong operation. Fix: change the banner string to describe renaming. Uses non-stdlib `cli_helper`/`amazing_print` (consistent with the tree). Status: open (verified; not filed, 3-issue cap reached this visit).
- Dependencies-audit non-findings (cleared, not re-filed): removed-API grep clean (no File.exists?/Dir.exists?/Fixnum/Bignum/URI.escape/.untaint/`$SAFE`/multi-arg ERB.new/`Kernel#open("http…")`); `ruby -wc` only benign "ambiguous first argument" warnings (disable.rb:26, github_open_pr.rb, jira_open.rb, summarize_file.rb); uninstalled personal gems are expected tool-script deps, not relics; the `cli_helper` require "failure" was a `ruby -e` probe artifact (`$0 == "-e"` breaks `Pathname.new($0).realpath`), works under real invocation.
- Triage non-finding: the Dependabot alerts API still returns 403 "disabled for this repository" (unchanged). Orphaned branch `dependabot/bundler/templates/sinatra_app/tzinfo-1.2.11` (bump 1.2.5→1.2.11) has no open PR and no longer applies — the `templates/sinatra_app/Gemfile.lock` it targeted was removed by #13. Stranded `bomber/*` branches: `bomber/readme-index-complete` carries #25's e48c57a; the rest are 0-ahead of develop.
- Status updates: F-6 PR'd (#24, open); F-14 PR'd (#26, Fixes #23, open); F-22/F-24 PR'd (#25, Fixes #19, open). None merged this visit — Dewayne has not reviewed #24/#25/#26 (zero reviews/comments on all three).

### 2026-11-10, third run

Quality audit (runtime defects, not style) at develop bc51825, in a sandbox. The dead-code quick scan of 543c66a..bc51825 (#17, #18, #20, #21) found nothing removable.

- F-38 (new, defect, verified): `shc.rb:16` `%w[-h --help].include? ARGV.shift` consumes the filename argument, so `ARGF.read` reads stdin. Repro: `ruby shc.rb in.txt </dev/null` gives NoMethodError at L42 (hangs with a tty). Fix: `(ARGV & %w[-h --help]).any?`. Status: issue [#32](https://github.com/MadBomber/scripts/issues/32).
- F-39 (new, defect, verified): `schema_diff.rb:35` calls `error` before `def error` at L53, so it raises NoMethodError (even on `-h`) outside a Rails root. Fix: move `def error` above the `DB_DIR` check. Status: issue [#33](https://github.com/MadBomber/scripts/issues/33).
- F-40 (new, defect, verified): `rultor_init_project.rb:71` usage heredoc interpolates `$options[:repo_dir].basename`, which is nil outside a git repo, so `-h` crashes. Fix: `&.basename`. L186 has the same nil dereference. Status: issue [#43](https://github.com/MadBomber/scripts/issues/43).
- F-41 (new, defect, verified): `filename_fu.rb:28-30` `nil[0,1]` with no args, before the `default_argv = ['--help']` at L101 can apply. Fix: `return parse_these_args(['--help']) if ARGV.empty?`. Status: issue [#34](https://github.com/MadBomber/scripts/issues/34).
- F-42 (new, defect, verified): `aip.rb:174,189` with no prompt does `nil + PROMPT_EXTNAME` instead of falling through to the fzf picker at L196. Fix: `return false if configatron.arguments.empty?` in `first_argument_is_a_prompt?`. Status: issue [#43](https://github.com/MadBomber/scripts/issues/43).
- F-43 (new, defect, verified): `rawk.rb:180,191` only match the Rails 2 `Completed in N.NNN` format, so a Rails 3+ log or an empty stdin gives ZeroDivisionError at L42. Fix: `abort` in `print_stats` when no request times are found. Status: the guard is open; supporting the modern log format (or retiring this vendored script) **needs Dewayne's decision**.
- F-44 (new, defect, verified in a sandbox): `normalize_pathnames.rb:59` ignores its arguments, so even `--help` renames everything under the cwd, including the cwd itself. Status: **needs Dewayne's decision** (a `-h` guard alone is small; a confirmation or dry-run changes how it is invoked).
- F-45 (new, defect, verified with the sandbox blocking it): `uninstall_all_gems.rb:8-15` ignores its arguments, so `-h` runs `gem up --system` and then uninstalls every gem. Proposed: require `--yes`. Status: **needs Dewayne's decision** (changes how it is invoked).
- F-46 (new, defect, verified): cli_helper scripts dereference missing required options: `copy_missing_files.rb:45,49`, `copy_modified_files.rb:47,51,55`, `get_db_relationships.rb:46`, `copy_csv_to_rdb.rb:66`. Fix: `&.` and `.to_s`. Status: open (obvious-yes PR; `copy_missing_files.rb` is also in #26, but at a distant line).
- F-47 (new, docs-vs-code, verified): `comment_require.rb:13` prints a literal `Usage: __FILE__`. Status: added to issue [#27](https://github.com/MadBomber/scripts/issues/27) by comment.
- F-48 (new, low, verified): `de_dog_da_log.rb:10` File.open(nil); `get_last_commit_message.rb:6-9` nil.each outside git; `compare_to_master.rb:20-21` Pathname.new(nil) when RR/MASTER_BRANCH_DIR is unset; `147.rb:150-156` any unknown argument (`-h`, a typo) toggles the scheme (the improver also found this); `imbibler.rb:173` takes `-h` as the output CSV name; stdin EOF gives nil.chomp at `blessing.rb:32`, `image_metadata.rb:32-33`, `release.rb:105` (the F-16 class). Status: open (low).
- F-49 (new, improvement, improver): `useful_gems.rb:264` still installs `github_api`, which Dewayne's own a7a6d18 dropped as unmaintained (it pins faraday < 2). Fix: swap it for `octokit`. Status: open (one line).
- F-50 (new, defect, improver, unverified): `set_upstream.rb:82` and `comment_shardfile.rb:86` `rescue Exception` inside per-repo loops, so Ctrl-C prints and moves on. Fix: `rescue StandardError` (or Octokit/Faraday errors). Status: unverified (proposed stub repro not run).
- F-51 (new, low, improver, unverified): `147.rb off` doesn't reset tab titles and hard-codes `bg=000000`. Status: unverified (needs a manual iTerm2 check).
- Cleared (not repo defects): categorize_gems `quirks_mode` (json 3.1 in a dependency); cli_helper 0.2.0 `abort_if_errors` nil.chomp on EOF stdin (external gem); create_changelog (environment); network and bundler/inline failures blocked by the sandbox; long-running scripts timing out.

### 2026-11-12

Dead-code audit at develop 86201ce, plus responder/triage/improver leads. Dewayne merged #24/#25/#26 into develop on 2026-11-10 (merge commits, no comments). Three PRs opened this visit: #37 (S-2), #35 (F-38, Fixes #32), #36 (F-31, Fixes #30). Issues filed: #38, #39, #40.

- Status updates: F-6 merged (#24). F-14 merged (#26; #23 stays open until develop reaches main). F-22/F-24 merged (#25; #19 likewise). F-38 PR'd ([#35](https://github.com/MadBomber/scripts/pull/35)). F-31 PR'd ([#36](https://github.com/MadBomber/scripts/pull/36)); F-32/F-33 stay open (other scripts, not covered by #36).
- S-2: security finding, details held locally. Status: PR'd ([#37](https://github.com/MadBomber/scripts/pull/37)).
- S-5: security finding, details held locally. Status: open (low).
- F-10, F-17, F-18, F-23 (re-verified at 86201ce): unchanged, each referenced only by itself and one README row. Status: needs Dewayne's decision (retire or keep).
- F-52 (new, dead code): `lib/bindings/utils.js`, `lib/tom-select/`, `lib/vis-9.1.2/` (~740 KB, 5 files) are pyvis HTML support files committed in 5d4554e ("adding end of year leftovers"). Nothing in the repo or in `~/scripts` loads them (`git grep -e tom-select -e vis-network -e vis-9 -e bindings/utils` outside lib/ is empty; only tracked HTML is templates/html5.html). Only `lib/find_repo_dir.rb` in lib/ is used. Status: needs Dewayne's decision (delete, or keep and .gitignore if he still generates pyvis graphs into ~/scripts).
- F-53 (new, low, dead code): `-v/--verbose` documented and parsed but never read in `csv_reader.rb` (verbose? L36), `docx_style_fixer.rb` (L29), `new.rb` (L29); `csv2json.rb:17` `header?` never called. Status: open (low; removing `-v` changes the documented CLI, so not obvious-yes).
- F-54 (new, defect, verified): extensionless `json` script calls `Pathname#basebane` at L25, so `ruby json` (usage) raises NoMethodError. Status: issue [#38](https://github.com/MadBomber/scripts/issues/38).
- F-55 (new, defect, verified): `wl` sets `wl=$1` unexported then reads `ENV["wl"]`, so it never prints a word. Status: issue [#39](https://github.com/MadBomber/scripts/issues/39).
- F-56 (new, defect, verified in sandbox): `copy_latest_voice_memo.rb -h` creates `./-h/` and copies the newest recording into it. Status: issue [#40](https://github.com/MadBomber/scripts/issues/40).
- F-57 (new, defect, responder, verified): `mbox_finder.rb` splits on `"\r"`, leaving a leading `\n` on each line, so `start_with?('Subject'/'Date')` never matches and the header prints blank. Separate from #29; could fold into the #29 PR with `each_line`/`chomp`. Status: open.
- F-58 (new, defect, improver, verified): `lflf2lf` uses `sed 's/\n\n/\n/g'`, which never sees two newlines (sed is line-at-a-time), so the file is rewritten unchanged. Fix: `ruby -i -0777 -pe 'gsub(/\n\n/, "\n")'`. Status: open (behaviour choice: collapse pairs vs strip all blank lines).
- F-59 (new, docs, improver): `fw` and `sw` are byte-identical (`grep $@ ~/Documents/dictionary.txt`) though README describes them differently. Status: needs Dewayne's decision (delete one, or make `fw` "starts with").
- Noticed by builders: `shc.rb` crashes on empty input (`.max` nil) and its `unless ARGV.empty?` guard is now redundant; `yaml2json.rb` crashes on an empty file (`chomp_the_hash!` on nil); `backup_envrc.rb` has unused `total_file_count`/`modified_file_count`. Status: open (low).

### 2026-11-12, second run

Security audit (focused) at develop 86201ce: the ~110 non-`.rb` / extensionless scripts the 2026-11-10 pass didn't read, the diff bc51825..86201ce (#24, #26; clean), and a first full-history secret scan (gitleaks, 541 commits: no real secrets; one false positive in minified vis-network.min.js). All script runs used `sandbox-exec`. Improver and triage also reported. No PRs built (review queue full).

- S-6: security finding, details held locally. Status: open (low; verified).
- S-7: security finding, details held locally. Status: open (low; verified by reading). Don't open a public PR touching `sql/create_system_users.sql` without reading the local note.
- S-8: security finding, details held locally. Status: open (verified in sandbox).
- F-60 (new, defect, verified in sandbox): `where:21` runs `system("#{here} --version")` on every PATH match, so `where uninstall_all_gems.rb` or `where normalize_pathnames.rb` would run those destructive scripts (they ignore arguments, F-44/F-45). Fix: argument guards in F-44/F-45 (preferred), or make `--version` opt-in. Status: needs Dewayne's decision; raises the priority of F-44/F-45.
- F-61 (new, defect, verified with a stub): `jira_update.sh:16` passes `--baseurl JIRASYNC_BASEURL` without `$`. Status: issue [#42](https://github.com/MadBomber/scripts/issues/42).
- F-62 (new, defect, verified with a stub): `update_jira.sh:8` `asof="YYYY-MM-DD$1"` writes `"last_sync":"YYYY-MM-DD2026-01-01"`. Status: issue [#42](https://github.com/MadBomber/scripts/issues/42).
- F-63 (new, low, verified by reading): `rb2html:38` declares `--outdir` but never reads it; L77 `gsub(".rb", ".html")` rewrites every `.rb` in the path. Status: open (needs outdir semantics confirmed).
- F-64 (new, low, verified in sandbox): `backup_dotfiles:16` globs without nullglob, so unmatched patterns print `cp: …/_*: No such file` every run. Fix: `shopt -s nullglob`. Status: held with S-8 (same loop).
- F-14 (extended, unverified): unquoted shell interpolation also in `ungem:59-60`, `rb2html:80`, `cf:39-41`, `erb2haml:27-33`, `new_branch.sh:41-53`, `convert_pdf_to_svg.sh:8-10` (the last also runs pdf2svg's stdout via backticks).
- F-65 (new, defect, improver, verified): `describe_gems.rb:27` requires `systemu`, unused and not installed, so it dies with LoadError on every run. `latest_gems.rb`, `markdown_to_docx.rb`, `summarize_file.rb` use it; swap for `Open3.capture3` (mind the return order: `markdown_to_docx.rb:126`). Status: issue [#41](https://github.com/MadBomber/scripts/issues/41). Best next PR: the one-line `describe_gems.rb` delete.
- F-66 (new, defect, improver, verified): `#!/bin/env ruby` shebang (doesn't exist on macOS) in five 2018-snapshot scripts incl. `convert_html_to_text.rb`, `latest_gems.rb`, `summarize_file.rb`; `rpp` uses `#!/usr/bin/ruby -w` (Apple's Ruby 2.6). README calls `rpp` a data-structure pretty printer; it is a source beautifier that rewrites in place. Status: issue [#72](https://github.com/MadBomber/scripts/issues/72) (the five top-level `#!/bin/env ruby` scripts; `rpp`/`rawk.rb` and the archive dirs not included).
- F-67 (new, defect, improver): `create_image_of` ignores its arguments, always sends a hard-coded otter prompt and writes `otter.png`; tracked without the executable bit; no README row. Status: open (small PR; touches S-6's file, so read the local note first).
- F-68 (new, improver): `relink_home_dirs.rb` / `replace_home_dots.rb` hard-code `/media/James` (crash/no-op); `latin2ascii.py` is Python 2; `erb2haml` uses systemu + `svn move`. Each referenced only by its README row. Status: needs Dewayne's decision (retire to `retirement_home/` or modernise).
- F-69 (new, low, triage, verified on main only): `ruler` (e746bef, main only) crashes without a TTY (`Terminal.size` nil → `ruler.rb:82` nil +) and uses the non-stdlib `terminal-size` gem; stdlib `io/console` would cover both. Status: held until main is synced into develop.
- Dead line: `aip.rb:368` `ptompt = %Q{prompt}`. Status: open (fold into the #43 PR).

### 2026-11-12, third run

Dependencies audit (Ruby 4.0/4.1 readiness) at develop 86201ce, plus improver leads. Queue full (#35/#36/#37), so no PRs; three issues filed.

- F-70 (new, defect, verified): `templates/Gemfile.erb:4-6` builds the `ruby` line from `RbConfig::CONFIG['prefix'].split('-').last`, which renders a path (rbenv 4.0.9, mise) or `"preview1"` (4.1 preview); `bundle check` rejects it. Fix: `ruby "<%= Gem.ruby_version %>"` (not `RUBY_VERSION`, which fails on previews). Status: issue [#44](https://github.com/MadBomber/scripts/issues/44).
- F-71 (new, defect, verified in sandbox): `razer_macro_daemon.rb` segfaults (exit 139) when it can't open the keyboard: the `ensure` blocks (L346-350, L427-431) call `set_device_mode`/`hid_close` on a null handle. Fix: guard each `ensure` body with `if ctrl_handle.to_i != 0` and `Array(extra_handles)`; don't `return` inside `ensure`. Status: open (small PR).
- F-72 (new, Ruby 4, verified): `epic_graph:27` and `copy_latest_voice_memo.rb:15` `require "cgi"`, removed in Ruby 4.0 (stub + warning). Both use only `CGI.escapeHTML`. Fix: `require "cgi/escape"`. Status: issue [#46](https://github.com/MadBomber/scripts/issues/46).
- F-73 (new, dependency, cause verified): `bundler_inline_example.rb:11` pins `cocoapods ~> 0.34.1` (activesupport < 4), which can't load on current Ruby; the script only uses `nap`. Fix: drop the cocoapods line. Status: open (small PR).
- F-74 (new, dependency/docs, verified): `comment_require.rb:39-61` `STDLIBS` lists ~12 libraries no longer in Ruby and many now-bundled gems, so it tags `require 'csv'`/`'ostruct'` as `# STDLIB` wrongly. Status: needs Dewayne's decision (how to label bundled gems); dropping the 12 dead names alone is a small PR.
- F-75 (new, defect, verified in sandbox): `install_gems_from_file.rb:10` runs `gem install amazing_print debug_me cli_helper ruby-progressbar` on every start, including `-h`. Fix: install only when missing. Status: open (small PR).
- F-76 (new, low, verified by reading): `create_changelog.rb:35` `require 'Pathname'` (capital P) fails on case-sensitive file systems. Fix: `require 'pathname'`. Status: open (one character).
- F-77 (new, defect, improver, verified in a temp repo): `changed_files` never fills `deleted_files`/`renamed_files`: `append_unique_existing_filepath` drops paths that don't exist, which is every deleted file and every `"old > new"` entry. Status: issue [#45](https://github.com/MadBomber/scripts/issues/45).
- F-78 (new, docs, improver, verified): nine README rows describe the wrong script: `acronym_simple.rb`, `bg2md.rb`, `changed_files`, `find_sev_used_in_a_project.rb`, `freshen_up.rb`, `gup.rb`, `headlines.rb`, `number`, `wcml_brf.rb` (same class as F-20/F-24). Status: open (small docs PR; low-risk self-merge candidate).
- F-79 (new, dead code, improver): `projxslt.rb` names a caller `ruby2html.rb` that doesn't exist; referenced only by itself and its README row; needs the uninstalled ruby-xslt gem. Status: needs Dewayne's decision (with F-10/F-17/F-18/F-23).
- F-66 extended: `rawk.rb:1` also uses `#!/usr/bin/ruby` (Apple Ruby 2.6).
- F-45 extended: on 3.4+/4.0, `gem uninstall -a -I -x` also removes bundled gems (csv, ostruct, logger, fiddle, readline, rexml), breaking ~16 scripts until reinstalled.
- Low, style: `aip.rb:149/159` redefines `FZF_OPTIONS`; `rpp:121` duplicated range in a character class; `ripfzf` header says it opens Sublime (only `ripfzfsubl` does).

### 2026-11-12, fourth run

Quality audit (logic defects in about 95 scripts no earlier finding names: top-level `.rb`, extensionless Ruby, shell, `lib/`, `templates/`) at develop 86201ce, plus improver leads. Everything ran under `sandbox-exec` against scratch copies or stubs. The queue was full (#35/#36/#37), so no PRs; three issues filed.

- F-80 (new, defect, **data loss**, verified in scratch): `move_fork_offline.rb` `validate!` (L56-67) uses the name as given. `f/` makes `File.symlink?` follow an already-moved fork, so `copy_fork` `rm_rf`s the only offline copy. `.` with `--force` wipes every other offline fork. Fix: reject names where `name != File.basename(name)` or the name is `.`/`..`, and `chomp('/')`. Status: issue [#47](https://github.com/MadBomber/scripts/issues/47).
- F-81 (new, defect, verified in a temp repo): `fix_perms.rb` `fix_dir` recurses into `.git/`, which strips `+x` from hooks. Run from a subdirectory, `git diff --name-only` returns root-relative paths, so tracked modes are never restored. Fix: skip `.git`, and run the restore step from the repo top level. Status: PR [#71](https://github.com/MadBomber/scripts/pull/71) open.
- F-82 (new, defect, verified): `prepend_relative_path_as_comment.rb` writes the header above the shebang and magic comment, drops the executable bit (tmp file plus `mv`), and prepends again on a rerun. Status: issue [#73](https://github.com/MadBomber/scripts/issues/73).
- F-83 (new, defect, verified with a stub Octokit; improver found it too): `github_clone_my_repos.rb:98-100` runs `mkdir` for forks even under `--dryrun`, so later runs treat the fork as cloned. Status: issue [#49](https://github.com/MadBomber/scripts/issues/49).
- F-84 (new, defect, verified with a stub brew): `brew_not_installed` keys on the first word of `brew bundle dump` lines (`brew`/`cask`/`tap`), so `--install` runs `brew install brew cask tap`. `--install` given as the first argument is taken as the path, and every run writes temp files into the cwd. Status: open (casks and taps need Dewayne's call).
- F-85 (new, defect, verified): `list_top_leve_gems.rb:19` and `create_install_gemset:36` count development dependencies, so 92 gems that are only someone's dev dependency are excluded. Fix: `.runtime_dependencies`. Status: open (one word per file).
- F-86 (new, defect, verified): `load_gems_from_file.rb:70-74` marks `(…, development)` dependency lines as installed. Status: open (one line).
- F-87 (new, defect, verified): `create_gems_used_report.rb` has five problems:
  - it includes development dependencies;
  - it prints all dependencies instead of the runtime `deps`;
  - it crashes on a `ruby` line in the Gemfile and drops `group` blocks;
  - it reports whichever gem version it saw last;
  - it raises NoMethodError when `pg` is missing.
  Status: open (small PR).
- F-88 (new, defect, verified): `image_rotater.rb:92` has a leftover `degrees += 15`, so the 2nd and 3rd files rotate by 105 and 120. L80 `gsub('.')` breaks paths that contain dots. The banner says "Resize". Status: open (small PR).
- F-89 (new, defect, verified): `resize_images.rb:87` opens the basename instead of `filepath`, so any image outside the cwd raises ENOENT. Status: PR [#70](https://github.com/MadBomber/scripts/pull/70) open.
- F-90 (new, defect, verified by running the regex): `s3_downloader.rb:171` turns the glob into an unanchored, unescaped regex (`*.txt` matches `x.txt.bak`), and L180 doesn't create subfolders. Fix: `File.fnmatch`, plus `dirname.mkpath`. Status: open (gated by aws-sdk).
- F-91 (new, defect, verified with Rack::MockRequest): `mock_web_app.rb` reads `request.body` twice, so logged bodies are empty, and a non-JSON body returns 500 despite the "always 200" promise. Status: open (small PR).
- F-92 (new, defect, verified): `magic_8_ball.rb:42` `system "say '#{answer}'"` breaks on "Don't count on it". Fix: `system("say", answer)`. Status: issue [#74](https://github.com/MadBomber/scripts/issues/74).
- F-93 (new, defect, verified with osacompile): `tell:19` pastes `$msg` into an AppleScript string literal, so any message containing `"` fails to compile. `echo`/`say` are unquoted. Fix: pass the text through `on run argv`. Status: open (small PR).
- F-94 (new, docs-vs-code, verified): `templates/big_ruby_project/README.md` describes the template being copied into the current project root, but `new.rb:153` copies the template's directory into a subdirectory instead. Status: needs Dewayne's decision (fixing the code changes every directory template).
- F-95 (new, low, verified): `find_stuff_between_blank_lines.rb:58-71` doesn't clamp its range: a match on line 0 prints `-1: LAST LINE`. Status: open.
- F-96 (new, Ruby 4/RubyGems 4.1, verified on 4.1.0preview1; improver found it too): `Gem::Specification.all` prints a NOTE on every call, at nine sites including `gemt:143`, the second most-used script. Fix: use the Enumerable methods or `.to_a`. Status: issue [#48](https://github.com/MadBomber/scripts/issues/48).
- F-97 (new, low, mixed):
  - `concat_consecutive_non_blank_lines.rb:28` gives each paragraph after the first a leading space;
  - `kwic.rb` doesn't escape output;
  - `json2yaml.rb:37` `gsub(extname)`;
  - `update_jira_json_txt.rb:34` crashes with `nil.split`;
  - `file_preable_template.rb:38` rewrites every `.txt` under the cwd on any invocation (it only survives because the lib_ruby `inlude_all?` typo crashes it on load);
  - `github_list_repos.rb:28` has no pagination;
  - `anagram_code.rb:11` has an unused `table`.
  Status: open (low).
- F-98 (new, improver, verified): `camels2snakes2camels.rb` (and the `c2s`/`s2c` wrappers) dies on load under Dewayne's `RUBYLIB`, because of the MadBomber/lib_ruby `mods/string_mods.rb:18` typo `inlude_all?`. It needs only `snakecase`/`camelcase`, and its usage text says "Cpnverts". No history use. Status: needs Dewayne's decision (make it stdlib-only, or retire it).
- F-99 (new, improver, dead code): `sgem` (a near-duplicate of `pushd_to_gem_directory`, untouched since 2018, 0 history uses) and `freshen_up.rb` (a subset of `freshen_up_git_repos.rb`). Status: needs Dewayne's decision (retire, with F-10/F-17/F-18/F-23/F-79).
- S-9: security finding, details held locally. Status: PR [#69](https://github.com/MadBomber/scripts/pull/69) open.

### 2026-11-13

Dead-code quick scan 86201ce..a35981f (Dewayne's #50-#67 plus the #35-#37 merges, 38 files). Nothing orphaned: #41 swapped systemu for Open3 cleanly in all four files, #33 moved `error` without leaving a copy, #31 left no `$errors`, #45's `append_unique` is used, and #28/#49/#44 left nothing behind. Changed Ruby files pass `ruby -c`; changed shell scripts pass `bash -n`. Triage re-checked F-46, F-57, F-66, F-71, F-73, F-75, F-76, F-78, F-82, F-85-F-88, F-91-F-93, F-95, F-97 at a35981f: all still present. Shipped this visit: S-9 (#69), F-89 (#70), F-81 (#71). Filed: F-66 (#72), F-82 (#73), F-92 (#74).

- F-10, F-17, F-18, F-23, F-79, F-99 (re-verified at a35981f): unchanged; each referenced only by itself and its README row. Status: needs Dewayne's decision (retire or keep).
- F-52 (re-verified at a35981f): the pyvis assets under `lib/` are still tracked and unreferenced. The improver traced them to 5d4554e ("adding end of year leftovers", 2026-01-03) as accidental pyvis output, with no reference anywhere in `~/sandbox/git_repos`. Status: needs Dewayne's decision (strong removal candidate).
- F-68 (note): after #41, `erb2haml:13,31` is the last live script needing systemu, which no longer loads on 4.1.0preview1. Status: needs Dewayne's decision (retire, or `system("svn", "move", ...)`).
- F-111 (new, improver, defect): #42 quoted only `--baseurl`. `jira_update.sh:17-20` and `update_jira.sh:40-44` still pass project, user, password and target unquoted, so a value with a space or `*` is split or globbed. Fix: quote them. Status: open (small PR; keep the title a plain quoting change).
- F-112 (new, improver, dead code): `aip.rb:370` `ptompt = %Q{prompt}` is never read; #43 did not fold it in. Status: open (one line; ride along with another PR).
- F-113 (new, improver, low): `gemt:199` comment is the last mention of `Specification.all` after #48. Status: open (ride-along).
- F-114 (new, builder, defect): `fix_perms.rb` `restore_git_perm` restores a mode only when `git diff` for the file is exactly 3 lines (mode-only change); a tracked executable that also has content edits stays 0644. Fix: read the mode from `git ls-files -s`. Status: open (after #71 merges).
- F-115 (new, builder, low): `resize_images.rb` help title says "Show the image metadata", the empty-input error says "No text files", and the metadata labels have typos ("tyoe:", "colorspace#"). Status: open (low; after #70 merges).

