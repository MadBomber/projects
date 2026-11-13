# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-13._ `develop` is green again (e55bd4d, 224 runs, 0 failures): madbot merged [#25](https://github.com/MadBomber/gem-skill/pull/25) (F-32 red tests) and [#27](https://github.com/MadBomber/gem-skill/pull/27) (F-10 env-sensitive tests) as low risk; [#26](https://github.com/MadBomber/gem-skill/pull/26) (F-17 verify honours max tokens) awaits Dewayne. Dewayne merged #9-#13 and #19 and opened his own PRs #20-#24 for issues #8, #14, #16, #17, #18. First adoption audit since 2026-11-06 ran (F-41..F-52); issues #28-#30 filed.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- `bundle skill` without a `Gemfile.lock` shows Bundler's error-report template (F-41, issue [#28](https://github.com/MadBomber/gem-skill/issues/28)). Land after Dewayne's #23 (same file).
- `gem skill setup` exits 0 on failure (F-43, issue [#29](https://github.com/MadBomber/gem-skill/issues/29)).
- Docs inaccuracies on the gem-skill/bundle-skill/index pages (F-48, F-50, issue [#30](https://github.com/MadBomber/gem-skill/issues/30)). Land after #21 and #23.
- Verify ignores `--max-tokens` (F-17). **PR open** [#26](https://github.com/MadBomber/gem-skill/pull/26).
- Gemspec lacks `documentation_uri`/`bug_tracker_uri` (F-45). Small, but touches the gemspec, so never low risk.
- Install tip says `bundle plugin install` where docs say `gem skill setup` (F-49). After Dewayne's #24 (same file).
- Covered by Dewayne's own open PRs, not bomberbot work: #8 (#20), #14 (#21), #16 (#22), #17 (#23), #18 (#24).

## Open bomberbot PRs

- [#26](https://github.com/MadBomber/gem-skill/pull/26) fix(verifier): cap verify output with max_tokens (F-17), opened 2026-11-13

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
