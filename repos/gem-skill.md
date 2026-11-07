# gem-skill

Generates Claude Code skills from Ruby gem docs.

## Links

- GitHub: https://github.com/MadBomber/gem-skill
- RubyGems: https://rubygems.org/gems/gem-skill
- Issues: https://github.com/MadBomber/gem-skill/issues

## Status

_Updated 2026-11-06._ First visit: adoption audit; the docs PR was given up at the review cap because the documented local-model path hits finding F-1, a Generator bug that is the next PR.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Fix local-model lookup so `lms/` and `apfel/` ids outside the static registry work (findings F-1), then rebase and open the docs branch [bomber/document-local-models](https://github.com/MadBomber/gem-skill/tree/bomber/document-local-models) (F-2).

## Open bomberbot PRs

- (none)
