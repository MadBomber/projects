# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-10._ #4 merged; F-16 docs-link fix merged into develop as low risk (#5); F-12 test fix open as [#9](https://github.com/MadBomber/gem-skill/pull/9) and the F-3/F-4 parser fix as [#10](https://github.com/MadBomber/gem-skill/pull/10); first security audit run (S-1..S-4 held locally); issues #6-#8 filed. develop is ~20 commits ahead of v0.2.2.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- S-1: security finding, details held locally (obvious-yes PR; do first next visit, neutral title).
- `rake test` leaves a stray `.claude/skills/my_gem` symlink in the repo root (findings F-12). **PR open** [#9](https://github.com/MadBomber/gem-skill/pull/9).
- `bundle skill` mis-parses value flags before the subcommand and ignores `--temperature` (findings F-3, F-4). **PR open** [#10](https://github.com/MadBomber/gem-skill/pull/10).
- Truncated `--verify` reply overwrites the cached skill (F-25, issue [#6](https://github.com/MadBomber/gem-skill/issues/6)); then F-17 (verify ignores max tokens).
- `gem skill` exits 0 on errors (F-26, issue [#7](https://github.com/MadBomber/gem-skill/issues/7)).
- `bundle skill` skips git-sourced gems (F-24, issue [#8](https://github.com/MadBomber/gem-skill/issues/8)).
- `ollama/` models can't be configured: `ENV_KEY_MAP` lacks `OLLAMA_API_BASE` (F-18).

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/gem-skill/pull/9) test: run gem command tests from a temp project dir (F-12), opened 2026-11-10
- [#10](https://github.com/MadBomber/gem-skill/pull/10) fix(bundle): parse skill flags with OptionParser, add --temperature (F-3, F-4), opened 2026-11-10

## Merged bomberbot PRs

- [#5](https://github.com/MadBomber/gem-skill/pull/5) docs: fix the cache layout link (F-16), merged into develop as low risk by bomberbot 2026-11-10 (3e384ad)
- [#4](https://github.com/MadBomber/gem-skill/pull/4) fix(plugin): require the command manager before registering (F-13), merged by Dewayne 2026-11-07
- [#3](https://github.com/MadBomber/gem-skill/pull/3) docs: document local models and request timeout (F-2), merged 2026-11-07
- [#2](https://github.com/MadBomber/gem-skill/pull/2) fix(generator): allow lms/apfel models missing from the registry (F-1), merged 2026-11-07
