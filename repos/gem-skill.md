# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-07 (third run)._ #3 merged; the Bundler plugin-load fix (F-13) is open as [#4](https://github.com/MadBomber/gem-skill/pull/4).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- `gem-skill_plugin.rb` raises `NameError` (uninitialized constant `Gem::CommandManager`) when Bundler loads RubyGems plugins (findings F-13). **PR open** [#4](https://github.com/MadBomber/gem-skill/pull/4).
- `rake test` leaves a stray `.claude/skills/my_gem` symlink in the repo root (findings F-12).
- Fix the broken `cache.md#metadatajson` link so `mkdocs build --strict` passes (findings F-16).

## Open bomberbot PRs

- [#4](https://github.com/MadBomber/gem-skill/pull/4) fix(plugin): require the command manager before registering (F-13), opened 2026-11-07
