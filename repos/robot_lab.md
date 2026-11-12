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

_Seventh visit (2026-11-12 07:08)._ Skipped building: review queue full (#38, #39, #40 still open, unreviewed). Ran the second **security** audit (S-4–S-9 held locally; F-66 public). Filed #46 (`wait_for_key` pipe leak), #47 (`doom_loop_threshold` 0 crash), #48 (Redis `:auto` backend never falls back, changes types). No issue needed a reply. `develop` 9d407dd still 31 behind `main`.

_Sixth visit (2026-11-12 04:18)._ Skipped building: review queue full (#38, #39, #40 still open, unreviewed). Ran a full-lib **quality** audit (F-52–F-58). Triage found released 0.6.0 breaks on ruby_llm 2.2.0 (tool approvals, MCP input): filed #43, a fix must ship from `main` (Dewayne's call). Filed #44 (token_budget unflagged after `reset_token_totals`, sync-safe) and #45 (`Robot#call` rescue `TypeError`). No issue needed a reply. `develop` 9d407dd still 31 behind `main`.

_Fifth visit (2026-11-12 03:16)._ Skipped building: review queue full (#38, #39, #40 open, green locally, unreviewed). Ran a full **dead-code** audit at develop 9d407dd incl. 16 dependent repos (F-46, F-47); filed #41 (test_helper `rescue LoadError` modifier, F-48) and #42 (dead `compact_if_over_context_window`, F-46). No issue needed a reply. `develop` 9d407dd still 31 behind `main`.

_Fourth visit (2026-11-12)._ Dewayne merged #26, #27 and #34 without comment, so the queue was empty. Ran the first **adoption** audit (F-24–F-30; all fixes wait for the sync except the simple_flow `tsort` root cause). Opened three independent `lib/` fixes, none low risk: **PR #40** (budget reservation leak on a failed call, F-31), **PR #39** (bad SKILL.md front matter crashes the catalog, F-40), **PR #38** (`ToolManifest#count` ignores its block, F-42). Filed #35, #36, #37. Visit 3's uncommitted brain write (F-22/F-23, #34) was folded into this record. `develop` 9d407dd still 31 behind `main`.

_Third visit (2026-11-10, same day)._ Tender rebased #26/#27 onto develop again (still unapproved, 2 open). Ran the never-run **quality** audit (flog/flay/reek, scoped to the 12 sync-safe lib files): surface is clean; new F-22/F-23 are both about the Asgard-owned gate being unrunnable from a bare clone (needs-decision → inbox). Opened **PR #34** (madbot/fix-delegation-spurious-wakeup → develop) fixing the `DelegationFuture#value` spurious-wakeup timeout, `Fixes #33`: **the bug IS provable** — `Thread#wakeup` forces an early return from `ConditionVariable#wait`, so F-16's "unprovable" note was wrong. Not self-merged (touches `lib/`), awaiting Dewayne. No new issues filed (everything new is needs-decision or already tracked). `develop` (0304617) still 31 behind `main`; only sync-safe files touched; docs PRs still wait for the sync.

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
- F-16 `DelegationFuture#value` spurious-wakeup timeout: issue [#33](https://github.com/MadBomber/robot_lab/issues/33), fixed on develop by #34 (9d407dd); closes with the sync or by hand.
- F-24/F-25 README tool examples: issue [#35](https://github.com/MadBomber/robot_lab/issues/35), after the sync.
- F-26 `tsort` on Ruby 4.1: issue [#36](https://github.com/MadBomber/robot_lab/issues/36); root fix in simple_flow's gemspec.
- F-43 `Narrator.enable!` double registration: issue [#37](https://github.com/MadBomber/robot_lab/issues/37) (narrator.rb sync-safe; test in a new file). Reproduced 2026-11-12 03:16 (`registrations=2`); first pick once the queue has room.
- F-48 test_helper `rescue LoadError` modifier never fires: issue [#41](https://github.com/MadBomber/robot_lab/issues/41), after the sync.
- F-46 dead `Robot#compact_if_over_context_window`: issue [#42](https://github.com/MadBomber/robot_lab/issues/42), after the sync.
- F-49 `Convergence` header says TF-IDF but uses TF only (comment fix, sync-safe; low-risk candidate). Bundle with F-63 (`error.rb:62` stale `ToolLoopError` message example).
- F-60 `token_budget` unflagged after `reset_token_totals`: issue [#44](https://github.com/MadBomber/robot_lab/issues/44). Sync-safe (`robot/budget.rb`, new test file); fix proven in scratch (1302 green). Touches the same file as PR #40, different hunks: build after #40 merges or stack on it.
- F-59 released 0.6.0 breaks on ruby_llm 2.2.0: issue [#43](https://github.com/MadBomber/robot_lab/issues/43). Fix ships from `main`; needs Dewayne's choice (cap `< 2.2` + 0.6.1, or adapt `ToolCallGating`).
- F-54 `Robot#call` rescue `TypeError` on bad context: issue [#45](https://github.com/MadBomber/robot_lab/issues/45), after the sync.
- F-67 `Memory#wait_for_key` pipe leak: issue [#46](https://github.com/MadBomber/robot_lab/issues/46). Sync-safe hunk; ~3 lines + test.
- F-68 `doom_loop_threshold` <= 0 crashes mid-run: issue [#47](https://github.com/MadBomber/robot_lab/issues/47). Sync-safe `initialize`; bundle F-69 dedupe if small.
- F-66 Redis `:auto` backend never falls back / changes types: issue [#48](https://github.com/MadBomber/robot_lab/issues/48). Part (b) `ping` fix after the sync.

## Open bomberbot PRs

- [#40](https://github.com/MadBomber/robot_lab/pull/40) `madbot/release-budget-on-failure` → develop. fix: release budget reservation when a run fails (F-31). **PR open**. lib, so not low risk. 1303 green, review clean round 1, merge of main conflict-free.
- [#39](https://github.com/MadBomber/robot_lab/pull/39) `madbot/skill-bad-front-matter` → develop. fix: skip SKILL.md files with unsafe YAML front matter (F-40). **PR open**. lib; also wraps `Psych::SyntaxError` as `ConfigurationError`. 1303 green, review clean.
- [#38](https://github.com/MadBomber/robot_lab/pull/38) `madbot/manifest-enumerable-count` → develop. fix: let ToolManifest#count use Enumerable#count (F-42). **PR open**. lib. 1303 green, review clean.

## Merged bomberbot PRs

- [#34](https://github.com/MadBomber/robot_lab/pull/34) fix: delegation future loops past spurious wakeups (F-16, #33). Merged by Dewayne 2026-11-10 as develop 9d407dd.
- [#27](https://github.com/MadBomber/robot_lab/pull/27) chore: stop tracking `.claude/settings.local.json` (S-3). Merged by Dewayne 2026-11-10 as develop e3efd58.
- [#26](https://github.com/MadBomber/robot_lab/pull/26) chore: remove stray `temp.md` and `logfile` (F-2). Merged by Dewayne 2026-11-10 as develop a5d653e.
- [#32](https://github.com/MadBomber/robot_lab/pull/32) test: add DelegationFuture unit tests (F-19). **Merged (low risk)** 2026-11-10 as develop 0304617. 9 new tests; suite 1291 → 1300.
- [#28](https://github.com/MadBomber/robot_lab/pull/28) docs: remove stale root agents.md (F-4). **Merged (low risk)** 2026-11-10 as develop 83b27c4.
