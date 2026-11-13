# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-13 06:00._ madbot opened [#31](https://github.com/MadBomber/gem-skill/pull/31) (gemspec doc/issue URIs, F-45), [#32](https://github.com/MadBomber/gem-skill/pull/32) (setup exit status, Fixes #29) and [#36](https://github.com/MadBomber/gem-skill/pull/36) (missing Gemfile.lock, Fixes #28); all await Dewayne with #26. Second security audit ran (F-53, F-54); issues #33-#35 filed. `develop` green at e55bd4d (224 runs, 0 failures); no test CI, so PR proof is the local suite.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- `bundle skill` without a `Gemfile.lock` shows Bundler's error-report template (F-41, issue [#28](https://github.com/MadBomber/gem-skill/issues/28)). **PR open** [#36](https://github.com/MadBomber/gem-skill/pull/36); merges clean with #23. Land after Dewayne's #23 (same file).
- `gem skill setup` exits 0 on failure (F-43, issue [#29](https://github.com/MadBomber/gem-skill/issues/29)). **PR open** [#32](https://github.com/MadBomber/gem-skill/pull/32).
- Docs inaccuracies on the gem-skill/bundle-skill/index pages (F-48, F-50, issue [#30](https://github.com/MadBomber/gem-skill/issues/30)). Land after #21 and #23.
- Verify ignores `--max-tokens` (F-17). **PR open** [#26](https://github.com/MadBomber/gem-skill/pull/26).
- Gemspec lacks `documentation_uri`/`bug_tracker_uri` (F-45). **PR open** [#31](https://github.com/MadBomber/gem-skill/pull/31).
- Install tip says `bundle plugin install` where docs say `gem skill setup` (F-49). Same file as #24 but a different hunk (line 119), so it need not wait.
- Covered by Dewayne's own open PRs, not bomberbot work: #8 (#20), #14 (#21), #16 (#22), #17 (#23), #18 (#24).
- `gem install --with-skill` does not stream (F-7, issue [#33](https://github.com/MadBomber/gem-skill/issues/33)). One line in `rubygems_plugin.rb` plus a test.
- `docs/skill-files.md` lists the wrong generation sources (F-55, issue [#34](https://github.com/MadBomber/gem-skill/issues/34)). Docs only; Pages deploys on `docs/**` pushes to develop.
- Unparseable numeric env vars become 0 (F-54, issue [#35](https://github.com/MadBomber/gem-skill/issues/35)). After #24.

## Open bomberbot PRs

- [#26](https://github.com/MadBomber/gem-skill/pull/26) fix(verifier): cap verify output with max_tokens (F-17), opened 2026-11-13; rebased onto e55bd4d 2026-11-13 06:00 (228/0)
- [#31](https://github.com/MadBomber/gem-skill/pull/31) chore(gemspec): add documentation and bug tracker URIs (F-45), opened 2026-11-13
- [#32](https://github.com/MadBomber/gem-skill/pull/32) fix(setup): exit 1 when a setup step fails (F-43, Fixes #29), opened 2026-11-13
- [#36](https://github.com/MadBomber/gem-skill/pull/36) fix(bundle): report missing Gemfile.lock, exit 1 (F-41, Fixes #28), opened 2026-11-13

## Merged bomberbot PRs

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
