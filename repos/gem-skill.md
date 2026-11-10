# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-10 (resumed visit)._ Three new PRs opened this visit: [#11](https://github.com/MadBomber/gem-skill/pull/11) verify-truncation fix (F-25, Fixes #6), [#12](https://github.com/MadBomber/gem-skill/pull/12) exit-codes fix (F-26, Fixes #7), [#13](https://github.com/MadBomber/gem-skill/pull/13) S-1 path validation (neutral title). All lib changes, none low risk, all awaiting Dewayne. #9/#10 still open and unapproved. First **dependencies** audit ran (F-23 verified; F-30/F-31 need decision). Issues [#14](https://github.com/MadBomber/gem-skill/issues/14) (F-27 docs) and [#15](https://github.com/MadBomber/gem-skill/issues/15) (F-18 ollama) filed. develop unmoved at 3e384ad.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- S-1: security finding, details held locally. **PR open** [#13](https://github.com/MadBomber/gem-skill/pull/13) (neutral title).
- `rake test` leaves a stray `.claude/skills/my_gem` symlink in the repo root (findings F-12). **PR open** [#9](https://github.com/MadBomber/gem-skill/pull/9).
- `bundle skill` mis-parses value flags before the subcommand and ignores `--temperature` (findings F-3, F-4). **PR open** [#10](https://github.com/MadBomber/gem-skill/pull/10).
- Truncated `--verify` reply overwrites the cached skill (F-25, issue [#6](https://github.com/MadBomber/gem-skill/issues/6)). **PR open** [#11](https://github.com/MadBomber/gem-skill/pull/11). Then F-17 (verify ignores max tokens) — the upstream cause; good next follow-up in the same file.
- `gem skill` exits 0 on errors (F-26, issue [#7](https://github.com/MadBomber/gem-skill/issues/7)). **PR open** [#12](https://github.com/MadBomber/gem-skill/pull/12).
- `bundle skill` skips git-sourced gems (F-24, issue [#8](https://github.com/MadBomber/gem-skill/issues/8)). Deferred this visit (collides with #13 in lockfile.rb); stack on #13 or build next visit.
- `ollama/` models can't be configured: `ENV_KEY_MAP` lacks `OLLAMA_API_BASE` (F-18, issue [#15](https://github.com/MadBomber/gem-skill/issues/15)).
- `docs/cache.md` + `how-it-works.md` describe removed metadata/Result fields (F-27, issue [#14](https://github.com/MadBomber/gem-skill/issues/14)).

## Open bomberbot PRs

- [#13](https://github.com/MadBomber/gem-skill/pull/13) Validate gem names and versions before building cache paths (S-1, neutral title), opened 2026-11-10
- [#12](https://github.com/MadBomber/gem-skill/pull/12) fix(cli): exit 1 on gem skill error paths (F-26, Fixes #7), opened 2026-11-10
- [#11](https://github.com/MadBomber/gem-skill/pull/11) Fix verify overwriting cache with truncated skill (F-25, Fixes #6), opened 2026-11-10
- [#10](https://github.com/MadBomber/gem-skill/pull/10) fix(bundle): parse skill flags with OptionParser, add --temperature (F-3, F-4), opened 2026-11-10
- [#9](https://github.com/MadBomber/gem-skill/pull/9) test: run gem command tests from a temp project dir (F-12), opened 2026-11-10

## Merged bomberbot PRs

- [#5](https://github.com/MadBomber/gem-skill/pull/5) docs: fix the cache layout link (F-16), merged into develop as low risk by bomberbot 2026-11-10 (3e384ad)
- [#4](https://github.com/MadBomber/gem-skill/pull/4) fix(plugin): require the command manager before registering (F-13), merged by Dewayne 2026-11-07
- [#3](https://github.com/MadBomber/gem-skill/pull/3) docs: document local models and request timeout (F-2), merged 2026-11-07
- [#2](https://github.com/MadBomber/gem-skill/pull/2) fix(generator): allow lms/apfel models missing from the registry (F-1), merged 2026-11-07
