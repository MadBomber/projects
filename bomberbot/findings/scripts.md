# Findings: scripts

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | Dependabot alert review plus a grep for removed Ruby APIs; no Gemfile at repo root, so no bundle-audit. |
| dead code | 2026-11-07 (quick scan only) | Quick scan of 73d6c82..968704b (only #13's template changes); nothing removable. No full dead-code audit yet. |
| dependencies | 2026-11-06 (partial) | `ruby -wc` sweep of all 225 tracked Ruby files, plus a grep for APIs removed in Ruby 3.x and 4.x. |
| adoption | n/a | Skipped for this repo by rule. |
| quality | never | |

Dead-code scan last covered commit: 968704b (develop, 2026-11-07)

## Findings

### 2026-11-06

- S-1: security finding, details held locally. Status: merged ([#13](https://github.com/MadBomber/scripts/pull/13), 2026-11-07; on develop, not yet on main).
- F-1: `templates/sinatra_app` shipped a stale Gemfile.lock and a `ruby "2.3.1"` pin, plus the unused refile gem that holds sinatra at 1.4. As a result, generated apps failed `bundle install`. Status: merged ([#13](https://github.com/MadBomber/scripts/pull/13), 2026-11-07).
- F-2: `Fixnum` (removed in Ruby 3.2) is still used in `get_next_parameter` in `docx_to_txt.rb:63`, `rultor_init_project.rb:91` and `s3_uploader.rb:82`. This finishes Dewayne's own 2e3de64, which fixed clone_all.rb and release.rb. The rultor_init_project and s3_uploader call sites can be reached through `-o`/`-r`/`-u`. The docx_to_txt call site is dead (`get_out_filename` is never called). Status: PR'd ([#14](https://github.com/MadBomber/scripts/pull/14)); used `Integer == param_index.class` to match 2e3de64. Best next PR: swap to `param_index.is_a?(Integer)`. Note that the docx scripts need a local `docx_helpers` lib that isn't in the repo, so they can't be seen running here.
- F-3: `docx_style_lister.rb:106` uses `File.exists?`, which was removed in Ruby 3.2, and raises NoMethodError on every run that has arguments. Status: PR'd ([#14](https://github.com/MadBomber/scripts/pull/14)).
- F-4: `imbibler.rb:29,113` uses `URI.escape` and the open-uri `Kernel#open(url)`, both removed in Ruby 3.0. Status: open. The fix needs `URI.open` plus an escape replacement (`URI::RFC2396_PARSER.escape`), and the script scrapes a website, so seeing it run means a network GET.
- F-5: `YAML.load` on `gem spec` output in `describe_gems.rb:50`, `squish_gem_list.rb:89` and `useful_gems.rb:494`. Under Psych 4 this likely raises on the `!ruby/object:Gem::Specification` tags. Status: unverified.
- F-6: `templates/sinatra_app/app.rb:15` has `raise APP_BIND, ...`, which raises NameError. It should be `raise MissingSystemEnvironmentVariable`. Status: open, verified by the #13 code review.
- F-7: `templates/sinatra_app/Gemfile` has no `rake` gem, so `bundle exec rake` in a generated app fails. Status: open.
- F-8: `templates/sinatra_app` uses thin, whose eventmachine dependency doesn't compile on Ruby 4.1.0-preview1, and the template also uses the end-of-life `sass` gem. Status: needs Dewayne's decision (switch `:server` to puma and drop thin; drop sass or move to sass-embedded; or wait for upstream).
- F-9: `new.rb:152` copies directory templates with an unquoted `system "cp -R #{...}"`, which breaks when the cwd contains spaces. `FileUtils.cp_r` would fix it. Status: open.
- F-10: `s3_uploader.rb` is a self-described "TODO: complete this stub". It could be removed, but nobody has checked whether anything uses it. Status: unverified.

### 2026-11-07

No audit ran: the backlog still had 3 or more verified open findings (F-4, F-6, F-7, F-9), and the 2026-11-06 audits are 1 day old. A quick dead-code scan of 73d6c82..968704b found nothing removable.

- F-4 re-confirmed: `imbibler.rb:29,113` still calls `URI.escape` and the open-uri `Kernel#open(url)`. The #14 code review flagged it again. Status: open, verified. Best next Ruby-compat PR.
- F-10 note: #14 got `s3_uploader.rb` past option parsing. It then stops at `hipchat_room_id`, which is undefined, and it requires `hipchat_s3`, which isn't installed. HipChat itself was shut down in 2019. This adds weight to the removal case, though a removal still needs evidence that nothing uses it. Status: unverified.
- F-11: `get_next_parameter` is copied into 7 scripts (clone_all, release, rultor_init_project, s3_uploader, docx_to_txt, docx_rm_blank_paragraphs, csv_reader). In the copies that take a String, `ARGV.find_index` can return nil and crash on `nil + 1`, but every current caller checks `ARGV.include?` first, so nothing triggers it today. Status: open (low; consolidating the copies into `lib/` is a design choice, not an obvious-yes PR).
