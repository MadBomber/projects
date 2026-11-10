# robot_lab

Ruby framework for building and orchestrating multi-robot LLM workflows on top of
ruby_llm: robots, networks, tools, MCP, memory, hooks and budgets, Ractor parallelism.
Released 0.6.0 (RubyGems), Ruby `>= 4.0.9` on `main`.

## Links

- GitHub: https://github.com/MadBomber/robot_lab
- RubyGems: https://rubygems.org/gems/robot_lab
- Docs: https://madbomber.github.io/robot_lab (mkdocs, `deploy-github-pages.yml`)
- Issues: https://github.com/MadBomber/robot_lab/issues

## Status

_First visit (2026-11-10, resumed after a spend-limit cut-off)._ Opened #26 and #27, merged #28 (stale `agents.md`) as low risk, filed #29-#31, and commented on #21. `develop` (now 83b27c4) is still 31 commits behind `main` (0.6.0), so until Dewayne syncs it bomberbot only touches files that are identical on both branches. No test CI (#30). The docs deploy also runs on pushes to `develop`, so `docs/` PRs wait for the sync.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- Sync `main` into `develop` (Dewayne, see `dewayne.md`). It unblocks almost everything below.
- F-8 `docs/llms.txt`, after the sync (written against 0.6.0).
- F-6 test CI: issue [#30](https://github.com/MadBomber/robot_lab/issues/30), after the sync.
- F-9 `documentation_uri`: issue [#31](https://github.com/MadBomber/robot_lab/issues/31) (gemspec, so Dewayne reviews).
- F-7 extension gems pin `robot_lab ~> 0.3.0`: issue [#29](https://github.com/MadBomber/robot_lab/issues/29) (needs releases of the extension gems).
- F-2 tracked `examples/**/*.log` files, after the sync.

## Open bomberbot PRs

- [#26](https://github.com/MadBomber/robot_lab/pull/26) `bomberbot/remove-stray-files` → develop. chore: remove stray `temp.md` and `logfile` (F-2). **PR open.** Not self-merged because `logfile` isn't docs or tests.
- [#27](https://github.com/MadBomber/robot_lab/pull/27) `bomberbot/untrack-local-settings` → develop. chore: untrack `.claude/settings.local.json` and ignore it (S-3). **PR open.** Has a before-merge step.

## Merged bomberbot PRs

- [#28](https://github.com/MadBomber/robot_lab/pull/28) docs: remove stale root agents.md (F-4). **Merged (low risk)** 2026-11-10 as develop 83b27c4.
