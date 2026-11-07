# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-07 (second run)._ #2 merged; the local-model docs (F-2) are open as [#3](https://github.com/MadBomber/gem-skill/pull/3).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Document local models and the request timeout (findings F-2): **PR open** [#3](https://github.com/MadBomber/gem-skill/pull/3).
- `gem-skill_plugin.rb` raises `NameError` (uninitialized constant `Gem::CommandManager`) when Bundler loads RubyGems plugins (findings F-13).
- `rake test` leaves a stray `.claude/skills/my_gem` symlink in the repo root (findings F-12).
- Fix the broken `cache.md#metadatajson` link so `mkdocs build --strict` passes (findings F-16).

## Open bomberbot PRs

- [#3](https://github.com/MadBomber/gem-skill/pull/3) docs: document local models and request timeout (F-2), opened 2026-11-07
