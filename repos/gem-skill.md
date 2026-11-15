# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-15 05:20._ Queue full (#44-#46 open, unapproved), so no new PRs; first full dead-code audit ran and issues #47-#49 were filed (platform gems in Gemfile.lock, e2e script, version sort). `develop` unchanged at f23b7fb.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Install tip says `bundle plugin install` where docs say `gem skill setup` (F-49). **PR open** [#44](https://github.com/MadBomber/gem-skill/pull/44).
- Empty `GEMSKILL_MODEL` becomes model `""` (F-62, issue [#41](https://github.com/MadBomber/gem-skill/issues/41)). Two lines in `generator.rb` plus an ENV save/restore test.
- `Validation.valid_version?` accepts padded versions (F-53, issue [#42](https://github.com/MadBomber/gem-skill/issues/42)).
- CHANGELOG `[Unreleased]` stale `with_params` line, duplicate `### Changed`, missing `Env`/`max_tokens:` entries (F-51, F-61, issue [#43](https://github.com/MadBomber/gem-skill/issues/43)). Land after this visit's PRs, which each add a `### Fixed` line.
- Platform-suffixed gems in Gemfile.lock dropped or mis-versioned (F-70, issue [#47](https://github.com/MadBomber/gem-skill/issues/47)). One regex in `lockfile.rb:32` plus tests.
- `scripts/e2e_test` always aborts "Runner failed" (F-66, issue [#48](https://github.com/MadBomber/gem-skill/issues/48)). One-line script fix.
- `gem skill list` sorts versions as strings (F-72, issue [#49](https://github.com/MadBomber/gem-skill/issues/49)). One line in `cache.rb` plus a test.
- Method-redefinition warnings in tests, now 9 (F-21). Test-only.
- Shadowed exception rescues at `fetcher.rb:113` (safe deletion, F-63) and `gem_command.rb:374` (F-35, narrowing is a call).
- Docs describe `gem skill setup` as plugin-only in `docs/index.md:34`, `docs/installation.md:18`; README says "once per machine" (:87) and "re-run after upgrading" (:208). Noticed by #44's builder; the upgrade half is F-42 (needs decision).

## Open bomberbot PRs

- [#44](https://github.com/MadBomber/gem-skill/pull/44) fix(cli): point the install tip at gem skill setup (F-49), opened 2026-11-15; suite 260/0; review hit cap, docs-wording leftovers only
- [#45](https://github.com/MadBomber/gem-skill/pull/45) fix(frontmatter): strip an empty frontmatter block (F-58), opened 2026-11-15; suite 269/0; review hit cap, round 3 fixed
- [#46](https://github.com/MadBomber/gem-skill/pull/46) fix(verifier): stream the verify request (F-60), opened 2026-11-15; suite 261/0; review hit cap, wording only

## Merged bomberbot PRs

- [#36](https://github.com/MadBomber/gem-skill/pull/36) fix(bundle): report missing Gemfile.lock, exit 1 (F-41, Fixes #28), squash-merged by Dewayne 2026-11-13
- [#32](https://github.com/MadBomber/gem-skill/pull/32) fix(setup): exit 1 when a setup step fails (F-43, Fixes #29), squash-merged by Dewayne 2026-11-13
- [#31](https://github.com/MadBomber/gem-skill/pull/31) chore(gemspec): add documentation and bug tracker URIs (F-45), squash-merged by Dewayne 2026-11-13
- [#26](https://github.com/MadBomber/gem-skill/pull/26) fix(verifier): cap verify output with max_tokens (F-17), squash-merged by Dewayne 2026-11-13
- [#27](https://github.com/MadBomber/gem-skill/pull/27) test: pass with GEMSKILL_DIR or GEMSKILL_PROJECT_DIR set (F-10), merged into develop as low risk by madbot 2026-11-13 (e55bd4d)
- [#25](https://github.com/MadBomber/gem-skill/pull/25) test: make stub_generator cache so install links for real (F-32, red develop), merged into develop as low risk by madbot 2026-11-13 (03434e2)
- [#19](https://github.com/MadBomber/gem-skill/pull/19) fix(config): apply OLLAMA_API_BASE/OLLAMA_API_KEY to RubyLLM (F-18, Fixes #15), merged by Dewayne 2026-11-13
- [#13](https://github.com/MadBomber/gem-skill/pull/13) Validate gem names and versions before building cache paths (S-1), merged by Dewayne 2026-11-10
- [#12](https://github.com/MadBomber/gem-skill/pull/12) fix(cli): exit 1 on gem skill error paths (F-26, Fixes #7), merged by Dewayne 2026-11-10
- [#11](https://github.com/MadBomber/gem-skill/pull/11) Fix verify overwriting cache with truncated skill (F-25, Fixes #6), merged by Dewayne 2026-11-10
- [#10](https://github.com/MadBomber/gem-skill/pull/10) fix(bundle): parse skill flags with OptionParser, add --temperature (F-3, F-4), merged by Dewayne 2026-11-10
- [#9](https://github.com/MadBomber/gem-skill/pull/9) test: run gem command tests from a temp project dir (F-12), merged by Dewayne 2026-11-10
- [#5](https://github.com/MadBomber/gem-skill/pull/5) docs: fix the cache layout link (F-16), merged into develop as low risk by bomberbot 2026-11-10 (3e384ad)
- [#4](https://github.com/MadBomber/gem-skill/pull/4) fix(plugin): require the command manager before registering (F-13), merged by Dewayne 2026-11-07
- [#3](https://github.com/MadBomber/gem-skill/pull/3) docs: document local models and request timeout (F-2), merged 2026-11-07
- [#2](https://github.com/MadBomber/gem-skill/pull/2) fix(generator): allow lms/apfel models missing from the registry (F-1), merged 2026-11-07
