# asgard

Thor-based task runner driven by .loki files; the other monitored repos, dev, and the `*_project` workspaces use it. Prefer `asgard <task>` where a repo has a `.loki`, otherwise `bundle exec rake test`. A change to a public method must not break dev; run dev's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-07 (third run)._ PR #9 adds the docs-site and issue-tracker links to the gemspec (F-14), from the first adoption audit; #8 merged. CI on develop is green.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-14: link the docs site and issue tracker from the gemspec metadata. **PR open** ([#9](https://github.com/MadBomber/asgard/pull/9))
- F-12: add an `llms.txt` built from the README and docs site. **PR open** ([#11](https://github.com/MadBomber/asgard/pull/11))

## Open bomberbot PRs

- [#11](https://github.com/MadBomber/asgard/pull/11) docs: add llms.txt index of the docs site (**PR open**, 2026-11-10)

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/asgard/pull/9) chore(gemspec): link the docs site and issue tracker (merged 2026-11-07)
- [#8](https://github.com/MadBomber/asgard/pull/8) refactor(watch): drop the Ractor#take fallback in join_worker (merged 2026-11-07)
- [#7](https://github.com/MadBomber/asgard/pull/7) fix(test): pass the suite when no locale is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/asgard/pull/6) Create schedule log directories owner-only (0700) (merged 2026-11-07)
