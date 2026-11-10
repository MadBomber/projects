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

_Second visit (2026-11-10, hours after the first)._ Tender rebased #26/#27 (still unapproved, 2 open). Ran the never-run **dependencies** audit (F-13–F-15): bundle-audit clean on `main` 0.6.0; develop's stale `ruby_llm ~> 1.12` carries 3 High ReDoS CVEs, resolved by the pending sync. Self-merged PR #32 (DelegationFuture unit tests) as low risk → develop 0304617 (1300 tests green, was 1291). Filed #33 (DelegationFuture spurious-wakeup timeout bug). `develop` (now 0304617) is still 31 behind `main`, so bomberbot only touches files identical on both branches; `docs/` PRs still wait for the sync.

_First visit (2026-11-10, resumed after a spend-limit cut-off)._ Opened #26 and #27, merged #28 (stale `agents.md`) as low risk, filed #29-#31, and commented on #21. No test CI (#30). The docs deploy also runs on pushes to `develop`, so `docs/` PRs wait for the sync.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- Sync `main` into `develop` (Dewayne, see `dewayne.md`). It unblocks almost everything below.
- F-8 `docs/llms.txt`, after the sync (written against 0.6.0).
- F-6 test CI: issue [#30](https://github.com/MadBomber/robot_lab/issues/30), after the sync.
- F-9 `documentation_uri`: issue [#31](https://github.com/MadBomber/robot_lab/issues/31) (gemspec, so Dewayne reviews).
- F-7 extension gems pin `robot_lab ~> 0.3.0`: issue [#29](https://github.com/MadBomber/robot_lab/issues/29) (needs releases of the extension gems).
- F-2 tracked `examples/**/*.log` files, after the sync.
- F-16 `DelegationFuture#value` spurious-wakeup timeout: issue [#33](https://github.com/MadBomber/robot_lab/issues/33) (sync-safe lib fix, but builder can't prove a spurious wakeup deterministically).

## Open bomberbot PRs

- [#26](https://github.com/MadBomber/robot_lab/pull/26) `bomberbot/remove-stray-files` → develop. chore: remove stray `temp.md` and `logfile` (F-2). **PR open**, rebased onto develop 2026-11-10 v2. Not self-merged because `logfile` isn't docs or tests.
- [#27](https://github.com/MadBomber/robot_lab/pull/27) `bomberbot/untrack-local-settings` → develop. chore: untrack `.claude/settings.local.json` and ignore it (S-3). **PR open**, rebased onto develop 2026-11-10 v2. Has a before-merge step.

## Merged bomberbot PRs

- [#32](https://github.com/MadBomber/robot_lab/pull/32) test: add DelegationFuture unit tests (F-19). **Merged (low risk)** 2026-11-10 as develop 0304617. 9 new tests; suite 1291 → 1300.
- [#28](https://github.com/MadBomber/robot_lab/pull/28) docs: remove stale root agents.md (F-4). **Merged (low risk)** 2026-11-10 as develop 83b27c4.
