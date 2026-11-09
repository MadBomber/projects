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
| quality | 2026-11-09 | FIRST full run. reek 6.6.0 over all top-level `*.rb` + `lib/` at develop 543c66a; ~99% style smells triaged out. Net-new: F-17 (find_unused_methods.rb broken), F-18 (seed_with_headers.rb missing requires), F-19 (79 files: 3+ consecutive puts vs heredoc). Removed-API sweep (File.exists?, Fixnum, URI.escape, .untaint, ERB safe-level) clean. |

Dead-code scan last covered commit: 543c66a (develop, 2026-11-09, fourth run; range 5fbd330..543c66a was only the #16 merge, nothing removable)

## Findings

### 2026-11-06

- S-1: security finding, details held locally. Status: merged ([#13](https://github.com/MadBomber/scripts/pull/13), 2026-11-07; on develop, not yet on main).
- F-1: `templates/sinatra_app` shipped a stale Gemfile.lock and a `ruby "2.3.1"` pin, plus the unused refile gem that holds sinatra at 1.4. As a result, generated apps failed `bundle install`. Status: merged ([#13](https://github.com/MadBomber/scripts/pull/13), 2026-11-07).
- F-2: `Fixnum` (removed in Ruby 3.2) is still used in `get_next_parameter` in `docx_to_txt.rb:63`, `rultor_init_project.rb:91` and `s3_uploader.rb:82`. This finishes Dewayne's own 2e3de64, which fixed clone_all.rb and release.rb. The rultor_init_project and s3_uploader call sites can be reached through `-o`/`-r`/`-u`. The docx_to_txt call site is dead (`get_out_filename` is never called). Status: merged ([#14](https://github.com/MadBomber/scripts/pull/14), 2026-11-07); used `Integer == param_index.class` to match 2e3de64. Best next PR: swap to `param_index.is_a?(Integer)`. Note that the docx scripts need a local `docx_helpers` lib that isn't in the repo, so they can't be seen running here.
- F-3: `docx_style_lister.rb:106` uses `File.exists?`, which was removed in Ruby 3.2, and raises NoMethodError on every run that has arguments. Status: merged ([#14](https://github.com/MadBomber/scripts/pull/14), 2026-11-07).
- F-4: `imbibler.rb:29,113` uses `URI.escape` and the open-uri `Kernel#open(url)`, both removed in Ruby 3.0. Status: open. The fix needs `URI.open` plus an escape replacement (`URI::RFC2396_PARSER.escape`), and the script scrapes a website, so seeing it run means a network GET.
- F-5: `YAML.load` on `gem spec` output in `describe_gems.rb:50`, `squish_gem_list.rb:89` and `useful_gems.rb:494`. Under Psych 4 this likely raises on the `!ruby/object:Gem::Specification` tags. Status: unverified.
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
