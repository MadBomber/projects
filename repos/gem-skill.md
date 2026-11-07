# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-07._ The F-1 local-model fix is open as [#2](https://github.com/MadBomber/gem-skill/pull/2); once it merges, the docs branch (F-2) gets rebased and opened next.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Fix local-model lookup so `lms/` and `apfel/` ids outside the static registry work (findings F-1): **PR open** [#2](https://github.com/MadBomber/gem-skill/pull/2). Then rebase and open the docs branch [bomber/document-local-models](https://github.com/MadBomber/gem-skill/tree/bomber/document-local-models) (F-2).
- `gem-skill_plugin.rb` raises `NameError` (uninitialized constant `Gem::CommandManager`) when Bundler loads RubyGems plugins (findings F-13).
- `rake test` leaves a stray `.claude/skills/my_gem` symlink in the repo root (findings F-12).

## Open bomberbot PRs

- [#2](https://github.com/MadBomber/gem-skill/pull/2) fix(generator): allow lms/apfel models missing from RubyLLM's registry (F-1), opened 2026-11-07
