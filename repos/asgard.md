# asgard

Thor-based task runner driven by .loki files; the other monitored repos, dev, and the `*_project` workspaces use it. Prefer `asgard <task>` where a repo has a `.loki`, otherwise `bundle exec rake test`. A change to a public method must not break dev; run dev's suite against the branch.

## Links

- GitHub: https://github.com/MadBomber/asgard
- RubyGems: https://rubygems.org/gems/asgard
- Issues: https://github.com/MadBomber/asgard/issues

## Status

_Updated 2026-11-10 (second visit)._ Review queue full: #16 (jobs start race / red CI), #12 (`--doctor` survives `exit`/`abort`), #11 (`docs/llms.txt`) all still open, green, MERGEABLE, none approved. Per the "3+ open bomberbot PRs" rule this was a tend-and-record visit: no new PR opened. Security audit ran (bundle-audit clean; one trust-boundary item S-2 held locally, needs decision). CI on develop stays red (the #16 race) until #16 merges.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-12: add an `llms.txt` built from the README and docs site. **PR open** ([#11](https://github.com/MadBomber/asgard/pull/11))

## Open bomberbot PRs

- [#11](https://github.com/MadBomber/asgard/pull/11) docs: add llms.txt index of the docs site (**PR open**, 2026-11-10)
- [#12](https://github.com/MadBomber/asgard/pull/12) fix(doctor): report exit or abort during .loki load (**PR open**, 2026-11-10; F-20)
- [#16](https://github.com/MadBomber/asgard/pull/16) fix(jobs): restart a job whose trampoline is still exiting (**PR open**, 2026-11-10; F-19)

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/asgard/pull/9) chore(gemspec): link the docs site and issue tracker (merged 2026-11-07)
- [#8](https://github.com/MadBomber/asgard/pull/8) refactor(watch): drop the Ractor#take fallback in join_worker (merged 2026-11-07)
- [#7](https://github.com/MadBomber/asgard/pull/7) fix(test): pass the suite when no locale is set (merged 2026-11-07)
- [#6](https://github.com/MadBomber/asgard/pull/6) Create schedule log directories owner-only (0700) (merged 2026-11-07)
