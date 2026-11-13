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

_Tenth visit (2026-11-13 02:45)._ Queue had only #70 (unreviewed). Opened **PR #73** (example 25 require order, Fixes #67), **merged (low risk)** as develop afd9131; **PR #74** (anonymous Hook namespace crash, F-88) and **PR #75** (`RobotResult.from_hash` restores `duration`/`delegated_by`, F-56), both lib so awaiting Dewayne. Quality audit v3 (delta to c700ba6): F-85–F-87. Filed #71 (budget reservation leaks on Timeout/Interrupt, F-85) and #72 (duplicate `hook_registries`, F-87). No issue needed a reply. `develop` afd9131 still 31 behind `main`.

_Ninth visit (2026-11-13 01:15)._ Dewayne merged #38, #39, #40 (rebase, no comment; develop cfae25c) and opened his own PRs #52–#65 for 14 bomberbot issues, so bomberbot's queue was empty. Opened **PR #66** (stale Convergence/ToolLoopError comments, F-49/F-63), **merged (low risk)** as develop c700ba6, and **PR #70** (`Ledger#reserve!` refuses exactly the remaining Float budget, F-81), lib so awaiting Dewayne. Ran the second **adoption** audit (first examples run: F-76–F-80) plus a dead-code delta to cfae25c. Filed #67, #68, #69. No issue needed a reply. `develop` c700ba6 still 31 behind `main`.

_Eighth visit (2026-11-12 08:04)._ Skipped building: review queue full (#38, #39, #40 still open, unreviewed, fourth visit waiting). Ran the third **dependencies** audit (F-71, F-72; F-15 now a verified resolve failure). Filed #49 (Rails example `last_result` returns oldest), #50 (`Network#parallel` YARD example fails), #51 (unused dev gems). No issue needed a reply. `develop` 9d407dd still 31 behind `main`.

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
- F-49/F-63 stale comments: [#66](https://github.com/MadBomber/robot_lab/pull/66), **merged (low risk)** 2026-11-13 as develop c700ba6.
- F-60 `token_budget` unflagged after `reset_token_totals`: issue [#44](https://github.com/MadBomber/robot_lab/issues/44). Sync-safe (`robot/budget.rb`, new test file); fix proven in scratch (1302 green). Touches the same file as PR #40, different hunks: build after #40 merges or stack on it.
- F-59 released 0.6.0 breaks on ruby_llm 2.2.0: issue [#43](https://github.com/MadBomber/robot_lab/issues/43). Fix ships from `main`; needs Dewayne's choice (cap `< 2.2` + 0.6.1, or adapt `ToolCallGating`).
- F-54 `Robot#call` rescue `TypeError` on bad context: issue [#45](https://github.com/MadBomber/robot_lab/issues/45), after the sync.
- F-67 `Memory#wait_for_key` pipe leak: issue [#46](https://github.com/MadBomber/robot_lab/issues/46). Sync-safe hunk; ~3 lines + test.
- F-68 `doom_loop_threshold` <= 0 crashes mid-run: issue [#47](https://github.com/MadBomber/robot_lab/issues/47). Sync-safe `initialize`; bundle F-69 dedupe if small.
- F-66 Redis `:auto` backend never falls back / changes types: issue [#48](https://github.com/MadBomber/robot_lab/issues/48). Part (b) `ping` fix after the sync.
- F-73 Rails example `RobotLabThread#last_result` returns the oldest result: issue [#49](https://github.com/MadBomber/robot_lab/issues/49). Sync-safe one-line `reorder` fix in `examples/18_rails/app/models/robot_lab_thread.rb`; first pick once the queue has room (docs/examples only, likely low risk).
- F-74 `Network#parallel` YARD example raises `NoMethodError`: issue [#50](https://github.com/MadBomber/robot_lab/issues/50), comment-only, after the sync.
- **Dewayne's own PRs #52–#65 (2026-11-12) cover #30, #31, #36, #37, #41, #42, #44–#51; don't touch their files.**
- F-81 `Ledger#reserve!` refuses exactly `remaining` (Float rounding): [#70](https://github.com/MadBomber/robot_lab/pull/70) **PR open**.
- F-77 `examples/25_history_search.rb` json-before-Bundler: issue [#67](https://github.com/MadBomber/robot_lab/issues/67). Fixed on develop by [#73](https://github.com/MadBomber/robot_lab/pull/73), **merged (low risk)** 2026-11-13 as afd9131; only bites on main, carries through the sync (hunk-safe).
- F-78 `examples/09_chaining.rb` bare robot goes to Anthropic: issue [#68](https://github.com/MadBomber/robot_lab/issues/68) (example half; lib half needs Dewayne).
- F-82 docs/comments say TF-IDF and quote a stale `ToolLoopError` message: issue [#69](https://github.com/MadBomber/robot_lab/issues/69), after the sync.
- F-72 unused dev gems (`state_machines*`, `lumberjack`): issue [#51](https://github.com/MadBomber/robot_lab/issues/51), after the sync.
- F-56 `RobotResult.from_hash` drops `duration`/`delegated_by`: [#75](https://github.com/MadBomber/robot_lab/pull/75) **PR open**.
- F-88 anonymous `Hook` subclass crashes `Hook.namespace`: [#74](https://github.com/MadBomber/robot_lab/pull/74) **PR open**. #74 and #75 each add a CHANGELOG `[Unreleased]` line; the second to merge needs a trivial rebase.
- F-85 budget reservation leaks when a run is interrupted (Timeout/Interrupt): issue [#71](https://github.com/MadBomber/robot_lab/issues/71). `robot/hooking.rb` (develop-only hunk, not in Dewayne's PRs); `ensure` + flag, test in `budget_leak_test.rb`. First pick next visit.
- F-87 duplicate `hook_registries` in `robot.rb`: issue [#72](https://github.com/MadBomber/robot_lab/issues/72), after #57/#59 merge.

## Open bomberbot PRs

- [#74](https://github.com/MadBomber/robot_lab/pull/74) `madbot/anon-hook-namespace` → develop. fix: nil namespace for anonymous Hook subclasses (F-88). **PR open**. lib, not low risk. Fails-before (1F/2E), 1312 green, review clean round 1.
- [#75](https://github.com/MadBomber/robot_lab/pull/75) `madbot/result-from-hash-fields` → develop. fix: restore duration and delegated_by in from_hash (F-56). **PR open**. lib, not low risk. Fails-before, 1308 green, review clean round 1.
- [#70](https://github.com/MadBomber/robot_lab/pull/70) `bomberbot/ledger-reserve-remaining` → develop. fix: let Ledger reserve exactly the remaining budget (F-81). **PR open**. lib, so not low risk. 1308 green, fails-before proven, review clean at the 3-round cap.

## Merged bomberbot PRs

- [#73](https://github.com/MadBomber/robot_lab/pull/73) fix(examples): load common before json in example 25 (F-77, Fixes #67). **Merged (low risk)** 2026-11-13 as develop afd9131. 2 lines.
- [#66](https://github.com/MadBomber/robot_lab/pull/66) docs: correct stale Convergence and ToolLoopError comments (F-49, F-63). **Merged (low risk)** 2026-11-13 as develop c700ba6. Comment-only, 3 lines.
- [#40](https://github.com/MadBomber/robot_lab/pull/40) fix: release budget reservation when a run fails (F-31). Merged by Dewayne 2026-11-12 as develop cfae25c.
- [#39](https://github.com/MadBomber/robot_lab/pull/39) fix: skip SKILL.md with unsafe YAML front matter (F-40). Merged by Dewayne 2026-11-12 (5dd891d, a06ba7a).
- [#38](https://github.com/MadBomber/robot_lab/pull/38) fix: let ToolManifest#count use Enumerable#count (F-42). Merged by Dewayne 2026-11-12 as develop 2740828.
- [#34](https://github.com/MadBomber/robot_lab/pull/34) fix: delegation future loops past spurious wakeups (F-16, #33). Merged by Dewayne 2026-11-10 as develop 9d407dd.
- [#27](https://github.com/MadBomber/robot_lab/pull/27) chore: stop tracking `.claude/settings.local.json` (S-3). Merged by Dewayne 2026-11-10 as develop e3efd58.
- [#26](https://github.com/MadBomber/robot_lab/pull/26) chore: remove stray `temp.md` and `logfile` (F-2). Merged by Dewayne 2026-11-10 as develop a5d653e.
- [#32](https://github.com/MadBomber/robot_lab/pull/32) test: add DelegationFuture unit tests (F-19). **Merged (low risk)** 2026-11-10 as develop 0304617. 9 new tests; suite 1291 → 1300.
- [#28](https://github.com/MadBomber/robot_lab/pull/28) docs: remove stale root agents.md (F-4). **Merged (low risk)** 2026-11-10 as develop 83b27c4.
