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

_Fifteenth visit (2026-11-15 08:36)._ Skipped building: review queue full (#95, #96, #97, #101 open, green, MERGEABLE, unreviewed). Ran **quality** v4 (delta c700ba6..7394a10 plus agent_skill/catalog, bus_messaging, mcp_management reads): F-113–F-117. Filed #102 (F-114), #103 (F-115), #104 (F-113). No issue needed a reply. `develop` 7394a10 still 31 behind `main`.

_Fourteenth visit (2026-11-15 05:53)._ No approvals; tender rebased #95 (green). Opened [#96](https://github.com/MadBomber/robot_lab/pull/96) (Fixes #90, F-53) and [#97](https://github.com/MadBomber/robot_lab/pull/97) (Fixes #91, F-106), both `lib/` so they wait for Dewayne, plus [#101](https://github.com/MadBomber/robot_lab/pull/101) (Fixes #92, F-107) stacked on #97. Ran the **dead-code** delta to 7394a10 (nothing new). Filed #98 (F-108), #99 (F-109), #100 (F-110). No issue needed a reply. `develop` 7394a10 still 31 behind `main`.

_Thirteenth visit (2026-11-15 02:17, resumed 03:53)._ Dewayne had merged #70, #74, #75 and his own #52–#65, #81–#89, so the queue was empty. Opened [#93](https://github.com/MadBomber/robot_lab/pull/93) (examples 09–11 amazing_print crash, F-103) and [#94](https://github.com/MadBomber/robot_lab/pull/94) (CHANGELOG backfill, F-102), both **merged (low risk)** (develop 7fe3145, 7394a10). Opened [#95](https://github.com/MadBomber/robot_lab/pull/95) (history compression orphans tool results, F-105), `lib/` so it waits for Dewayne. Ran **adoption** v3: the live docs site is now built from develop (F-101, Dewayne). Filed #90 (F-53), #91 (F-106), #92 (F-107). No issue needed a reply. `develop` 7394a10 still 31 behind `main`.

_Twelfth visit (2026-11-13 08:55)._ Skipped building: review queue full (#70, #74, #75 rebased onto develop afd9131 by the tender, green locally, unreviewed). Ran the **dependencies** v4 delta (no new findings; F-26 correction: the dev bundle masks the 4.1 tsort failure). Improver read bus_poller/network/task/tool: filed #78 (BusPoller strands queued messages after a failed delivery, F-94), #79 (ractor scheduler not shut down on failure, F-95), #80 (duplicate `Network#task` name silently dropped, F-96). No issue needed a reply. `develop` afd9131 still 31 behind `main`.

_Eleventh visit (2026-11-13 05:15)._ Skipped building: review queue full (#70, #74, #75 open, unreviewed, MERGEABLE). Ran a targeted **security** v3 audit (run_config, state_proxy, hook_context, agent_skill_matching, main's MCP files) plus a dead-code delta (nothing): F-90–F-93, S-11 held locally. Filed #76 (`HookContext#to_h` drops inherited readers, F-90) and #77 (`DotState` bracket access stores the wrong value, F-93). No issue needed a reply. `develop` afd9131 still 31 behind `main`.

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
- **Done on develop as of 2026-11-15** (issues close with the sync/release, since `Closes #N` only fires on `main`): F-6 #30, F-9 #31, F-16 #33, F-26 #36, F-43 #37, F-48 #41, F-46 #42, F-60 #44, F-54 #45, F-67 #46, F-68 #47, F-66 #48, F-73 #49, F-74 #50, F-72 #51, F-77 #67, F-78 example half #68, F-82 #69, F-85 #71, F-87 #72, F-90 #76, F-93 #77, F-94 #78, F-95 #79, F-96 #80, F-81, F-56, F-88, F-102, F-103.
- F-101 live docs site built from develop (pre-0.3.0 install requirements): Dewayne re-deploys from `main` and decides F-10. **Hold every `docs/` PR until then.**
- F-105 history compression orphans tool results: [#95](https://github.com/MadBomber/robot_lab/pull/95) **PR open**.
- F-53 async `Robot#delegate` never settles on a non-StandardError: issue [#90](https://github.com/MadBomber/robot_lab/issues/90), [#96](https://github.com/MadBomber/robot_lab/pull/96) **PR open**.
- F-106 `subscribe_pattern` glob leaves regex metacharacters live: issue [#91](https://github.com/MadBomber/robot_lab/issues/91), [#97](https://github.com/MadBomber/robot_lab/pull/97) **PR open**.
- F-107 `Memory#get(:a, :b, wait: N)` waits N per key: issue [#92](https://github.com/MadBomber/robot_lab/issues/92), [#101](https://github.com/MadBomber/robot_lab/pull/101) **PR open** (stacked on #97).
- F-108 one raising Memory subscriber drops the rest of the batch: issue [#98](https://github.com/MadBomber/robot_lab/issues/98). `memory.rb` `drain_notification_queue`, ~4 lines + test; stack on the memory PRs. First pick next visit.
- F-109 `Memory#set` bypasses reserved-key rules of `[]=`: issue [#99](https://github.com/MadBomber/robot_lab/issues/99). ~2 lines + 2 tests, same file.
- F-110 storing `:timeout` makes a blocking `get` raise `AwaitTimeout`: issue [#100](https://github.com/MadBomber/robot_lab/issues/100). ~3 lines inside `wait_for_key`.
- F-114 non-String SKILL.md `name` crashes the catalog and leaves it empty: issue [#102](https://github.com/MadBomber/robot_lab/issues/102). One guard in `AgentSkill#initialize` + test; hunk-check `agent_skill*.rb` vs main. Pair with F-41 (same file family).
- F-115 `Robot#with_bus` detaches a network robot from the shared BusPoller: issue [#103](https://github.com/MadBomber/robot_lab/issues/103). ~3 lines in `bus_messaging.rb#teardown_bus_channel` + test.
- F-113 catalog can't find a skill by folder name when `name` differs: issue [#104](https://github.com/MadBomber/robot_lab/issues/104). Index by `dir.basename` too.
- F-41 CRLF **or UTF-8 BOM** SKILL.md rejected as "missing 'name'": ~2 lines in `agent_skill.rb:53`, hunk-safe, reproduced 2026-11-15 by triage (LF ok, CRLF/BOM fail). Good independent pick.
- F-2 `examples/14_rusty_circuit/show.log` still tracked (identical on both branches; easy removal). Writers' Room `.log` files and root planning notes: Dewayne (inbox).
- F-8 `docs/llms.txt`, after the sync and F-101.
- F-104 five dead docs anchors (develop-only pages), after the sync and F-101.
- F-24/F-25 README tool examples: issue [#35](https://github.com/MadBomber/robot_lab/issues/35); correct on develop's ruby_llm 1.16, so after the sync.
- F-59 released 0.6.0 breaks on ruby_llm 2.2.x: issue [#43](https://github.com/MadBomber/robot_lab/issues/43), Dewayne's choice (still true on 2.2.2).
- F-7 extension gems pin `robot_lab ~> 0.3.0`: issue [#29](https://github.com/MadBomber/robot_lab/issues/29) (extension releases).

## Open bomberbot PRs

- [#96](https://github.com/MadBomber/robot_lab/pull/96) `madbot/delegate-settle-fatal` → develop. fix(robot): settle async delegate future on non-StandardError (F-53, Fixes #90). **PR open**. lib, not low risk. Fails-before/pass-after, 1341 green, review clean round 1, CI green.
- [#97](https://github.com/MadBomber/robot_lab/pull/97) `madbot/glob-escape-regex` → develop. fix(memory): treat regex metacharacters in subscribe_pattern globs literally (F-106, Fixes #91). **PR open**. lib, not low risk. Fails-before/pass-after, 1341 green, review clean round 1, CI green.
- [#101](https://github.com/MadBomber/robot_lab/pull/101) `madbot/get-wait-deadline` → `madbot/glob-escape-regex` (stacked on #97). fix(memory): treat multi-key get wait: as one total deadline (F-107, Fixes #92). **PR open**. Fails-before/pass-after, 1342 green, CI green; self-review only (no full /code-review round). Retarget to develop once #97 merges.

- [#95](https://github.com/MadBomber/robot_lab/pull/95) `madbot/compress-keep-tool-calls` → develop. fix: keep assistant tool calls with text during history compression (F-105). **PR open**. lib, not low risk. Fails-before/pass-after, 1341 green, review clean round 1, CI green, MERGEABLE. Rebased onto 7394a10 by the tender 2026-11-15 05:53 (b546016), CI green.

## Merged bomberbot PRs

- [#94](https://github.com/MadBomber/robot_lab/pull/94) docs: add missing CHANGELOG entries for merged fixes (F-102). **Merged (low risk)** 2026-11-15 as develop 7394a10. CHANGELOG only, +31.
- [#93](https://github.com/MadBomber/robot_lab/pull/93) fix(examples): load amazing_print before common (F-103). **Merged (low risk)** 2026-11-15 as develop 7fe3145. +7/−3.
- [#75](https://github.com/MadBomber/robot_lab/pull/75) fix: restore duration and delegated_by in from_hash (F-56). Merged (squash) by Dewayne 2026-11-13 as develop b45e351.
- [#74](https://github.com/MadBomber/robot_lab/pull/74) fix: nil namespace for anonymous Hook subclasses (F-88). Merged (squash) by Dewayne 2026-11-13 as develop 966ebd2.
- [#70](https://github.com/MadBomber/robot_lab/pull/70) fix: let Ledger reserve exactly the remaining budget (F-81). Merged (squash) by Dewayne 2026-11-13 as develop 10ee705.
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
