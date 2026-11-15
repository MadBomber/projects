# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-15 (08:17 visit)._ Dewayne merged everything: all five bomberbot PRs (#226–#229, #233), his #206–#222, and his #243–#254, which fixed every bot-filed issue except #240. develop moved 0d611ef → 5499744 and the suite is 2067/0/1 skip. With the queue empty, bomberbot opened [#255](https://github.com/MadBomber/aia/pull/255) (logs to stderr, Fixes #240), [#259](https://github.com/MadBomber/aia/pull/259) (orchestrate artifacts stay under the build dir, S-6) and [#260](https://github.com/MadBomber/aia/pull/260) (F-104 front-matter crash). It filed [#256](https://github.com/MadBomber/aia/issues/256) (F-150), [#257](https://github.com/MadBomber/aia/issues/257) (F-151) and [#258](https://github.com/MadBomber/aia/issues/258) (F-117). The security audit found S-6 and F-147..F-149.

_Prior (2026-11-13 08:48 visit)._ **Skipped new PRs: the review queue is full.** #226–#229 and #233 are unchanged and unapproved, and develop is still 0d611ef. The quality audit and the improver found three issues. Log lines default to stdout and mix into piped answers ([#240](https://github.com/MadBomber/aia/issues/240), F-144). Malformed `--mcp` JSON files fail badly ([#241](https://github.com/MadBomber/aia/issues/241), F-145). `TurnState` has a duplicated clear method ([#242](https://github.com/MadBomber/aia/issues/242), F-141).

_Prior (2026-11-13 08:00 visit)._ **Skipped new PRs: the review queue is full.** #226–#229 and #233 are unchanged and unapproved; develop is still 0d611ef and none of Dewayne's #206–#222 has merged. The dependencies audit found that the unit suite opens the user's real TrakFlow database ([#237](https://github.com/MadBomber/aia/issues/237), F-135; land before the #236 fix). Also filed [#238](https://github.com/MadBomber/aia/issues/238) (`--role` never validated, F-136) and [#239](https://github.com/MadBomber/aia/issues/239) (`/tools` with a description-less tool, F-137).

_Prior (2026-11-13 02:55 visit)._ **Skipped new PRs: the review queue is full.** #226–#229 and #233 are unchanged, unapproved and 0 behind develop, and develop is still 0d611ef. The adoption audit found that TrakFlow fails on a fresh install ([#236](https://github.com/MadBomber/aia/issues/236), F-116) and that the live docs site was rolled back to 2.0.0 by a develop deploy (F-119, needs Dewayne). Also filed [#234](https://github.com/MadBomber/aia/issues/234) (F-130) and [#235](https://github.com/MadBomber/aia/issues/235) (F-131).

_Prior (2026-11-13 02:05 visit)._ Nothing approved or merged; develop still 0d611ef. Bomberbot opened [#228](https://github.com/MadBomber/aia/pull/228) (F-69, #223), [#229](https://github.com/MadBomber/aia/pull/229) (F-103) and [#233](https://github.com/MadBomber/aia/pull/233) (F-101, #224); five bomberbot PRs now await review, so the next visit will skip new work. Filed #230 (F-111), #231 (F-112), #232 (F-62).

_Prior (2026-11-13 00:15 visit)._ Dewayne rebase-merged #183 and #188 into develop (now 0d611ef); #193 merged only into its stacked base, and his own #206 redoes it. He also opened 17 fix PRs, #206–#222, covering nearly every open issue. Bomberbot opened [#226](https://github.com/MadBomber/aia/pull/226) (F-68) and [#227](https://github.com/MadBomber/aia/pull/227) (F-88 removal). A test-CI workflow is built but blocked: the bots' gh token lacks the `workflow` scope (`bomberbot/plans/aia-test-ci.md`). Filed #223 (F-69), #224 (F-101) and #225 (F-50).

_Prior (2026-11-12 08:45 visit)._ **Skipped new PRs a fourth time: the review queue is full** (#183, #188, #193 unchanged, unapproved; develop still 22120fa). The improver found that **every stdio MCP server fails to start under ruby_llm 2.2** ([#203](https://github.com/MadBomber/aia/issues/203), F-100; also S-5, held locally, which constrains the fix). Also filed [#204](https://github.com/MadBomber/aia/issues/204) (`/config`, F-64+F-82) and [#205](https://github.com/MadBomber/aia/issues/205) (README model IDs and Quick Start key step, F-102). The dead-code audit found F-86..F-90.

_Prior (2026-11-12 07:30 visit)._ **Skipped new PRs a third time: the review queue is full.** #183, #188 and #193 are unchanged and unapproved, and develop is still 22120fa. The quality audit and the improver found that **cost and token metrics have been broken since the move to ruby_llm 2**: filed [#200](https://github.com/MadBomber/aia/issues/200) (F-80, prices) and [#201](https://github.com/MadBomber/aia/issues/201) (F-81, tokens). Also filed [#202](https://github.com/MadBomber/aia/issues/202) (a failing directive ends the chat, F-65). F-67 (1.x log truncated on upgrade) needs Dewayne.

_Prior (2026-11-12 06:16 visit)._ **Skipped new PRs again: the review queue is full.** #183, #188 and #193 are unchanged, MERGEABLE and unapproved, and develop is still 22120fa. Filed [#197](https://github.com/MadBomber/aia/issues/197) (zsh/fish completion broken, F-61), [#198](https://github.com/MadBomber/aia/issues/198) (`debug_me` runtime dep, F-58) and [#199](https://github.com/MadBomber/aia/issues/199) (`mcp_servers/README.md` drift, F-63). The dependencies audit is clean apart from F-58..F-60; F-59 (tty-table breaks under frozen string literals) needs Dewayne.

_Prior (2026-11-12 03:44 visit)._ **Skipped new PRs: the review queue is full.** The tender rebased #183, #188 and #193 onto 22120fa (all CLEAN, suites green), and none is approved yet. Filed [#194](https://github.com/MadBomber/aia/issues/194) (`--provider` ignored for bare slug names; F-19 was never actually merged, and its fix is stranded on `bomber/provider-bare-model-name`), [#195](https://github.com/MadBomber/aia/issues/195) (`--fuzzy` broken) and [#196](https://github.com/MadBomber/aia/issues/196) (`--require` spaces). The adoption audit found F-49..F-53 and S-3.


## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo. Rewritten 2026-11-15 after develop absorbed #206–#254; every older item not listed here is done.

- **Now in PRs (2026-11-15):** F-144 ([#240](https://github.com/MadBomber/aia/issues/240)) → **PR open** [#255](https://github.com/MadBomber/aia/pull/255); S-6 → **PR open** [#259](https://github.com/MadBomber/aia/pull/259); F-104 → **PR open** [#260](https://github.com/MadBomber/aia/pull/260). All three are lib, so none is low risk; each touches CHANGELOG `[Unreleased]`, so the second and third to merge need a CHANGELOG-only rebase.
- **Filed, ready for a PR:** [#256](https://github.com/MadBomber/aia/issues/256) (F-150, per-model run config: `robot_factory.rb`, `network_builder.rb`, `verification_network.rb`, about 15 lines); [#257](https://github.com/MadBomber/aia/issues/257) (F-151, uncastable `/temperature`: `configuration_directives.rb`, about 6 lines); [#258](https://github.com/MadBomber/aia/issues/258) (F-117, EBADF with no TTY: `validator.rb` + `variable_input_collector.rb`, about 15 lines).
- **Unfiled, verified on 5499744 by triage:** F-139 + F-148 (bash and fish completion `cd` in the user's shell; `aia_completion.bash:15-26`, `aia_completion.fish:11`; fish unverified, build them together); F-120 (five README blog links 404; lowercase slugs return 200; docs-only, a low-risk candidate once #255 merges, since both touch README); F-138 (`speech.rb:57` `~` never expanded and `system` result ignored, about 5 lines); F-147 (Tavily sample puts the key in the command string with empty `env`; sample + docs sentence; check the S-5 wording note in local notes first); F-149 (security.md "what aia writes" omits orchestrate build dir and `--dump`; only after #259 merges).
- **Removals, verified on 5499744:** F-132 (`max_tools`, about −4); F-71 (`ephemeral:`, about −4); F-133 (plugin `signature:`, about −8); F-87 (`decisions:`, about −10); F-108 (network shared memory, about −25, now unblocked); F-153 (stale `chat_loop.rb:229-230,303-304` comments, fold into the next chat_loop PR).
- **DRY refactors:** F-28 (`config.rb` `reset_section`/`merge_into_section`, about −10); F-36 (network_builder setup). F-31 and F-142 stay held under F-45/F-46 (`pipeline_orchestrator.rb`).
- **Docs:** F-49 (README config reference missing about 7 flags); F-152 (`examples/23_verify.sh` never shows `/verify <delta>`); F-57 (`lib/AGENTS.md`). F-83 and the docs half of #205 (`claude-sonnet-4` in `docs/`) wait on F-119 (docs deploy from develop).
- **Follow-ups noticed by builders:** `run_specialists_wave` prints `✓ <artifact>` even when `save_artifact` skipped (after #259); `--list-roles` shows an empty table for a role with no usable front matter, unlike `--list-skills` (after #260); `prompt_handler.rb:89-90,112-113,135-136` warn twice on stderr once #255 lands.
- **Blocked on Dewayne:** F-45/F-46 (main→develop sync), F-119 (docs deploy), F-140 (`/compare` provider), F-154 (`/verify` clamp), F-13, open issues #93/#146/#147/#148 (design).
- Builder notes: aia has no `exe/`; the executable is `bin/aia`. `.rubocop.yml` inherits `../.rubocop-base.yml`; copy it into `build/`. The `src/` clone checks out `main`, so base work on `origin/develop`. No `.ruby-version`; builders used global 4.1.0-preview1. No stacked PRs on aia.

## Open bomberbot PRs

- [#255](https://github.com/MadBomber/aia/pull/255) fix(logger): send aia and llm logs to stderr by default (F-144, Fixes #240). **PR open**; review clean in 1 round, suite 2069/0/1. Lib + docs, not low risk; changes a default (opt-back documented).
- [#259](https://github.com/MadBomber/aia/pull/259) fix(orchestrate): write each artifact under its build directory (S-6). **PR open**; review clean in 1 round, suite 2070/0/1. Lib, security, not low risk.
- [#260](https://github.com/MadBomber/aia/pull/260) fix(skills): ignore front matter that is not a mapping (F-104). **PR open**; review clean in 1 round, suite 2068/0/1. Lib, not low risk; also drops #228's now-redundant Hash guard.
- Tender (2026-11-15 08:17): no open bot PRs before this visit; deleted 11 merged bot branches. Kept `madbot/share-extract-model-id` (content on develop via #206) and `bomber/provider-bare-model-name` (superseded by #210) for Dewayne to delete.

## Merged bomberbot PRs

- [#226](https://github.com/MadBomber/aia/pull/226), [#227](https://github.com/MadBomber/aia/pull/227), [#228](https://github.com/MadBomber/aia/pull/228), [#229](https://github.com/MadBomber/aia/pull/229), [#233](https://github.com/MadBomber/aia/pull/233): squash-merged by Dewayne 2026-11-13 22:15–22:18 (→ 0eb1fa7, d5109c9, ffbf1fb, 9bcf2df, f58629e); he rewrote #227's title into conventional form

- [#188](https://github.com/MadBomber/aia/pull/188) refactor: remove write-only `TurnState#active_mcp_servers` (rebase-merged by Dewayne 2026-11-12 → 0d611ef, resolves #187/F-43)
- [#183](https://github.com/MadBomber/aia/pull/183) refactor(spawn): reuse `Crew.model_opts` (rebase-merged by Dewayne 2026-11-12 → b65fd22, resolves #181/F-29)
- [#193](https://github.com/MadBomber/aia/pull/193) shared `model_id_of` lookup: merged by Dewayne into its stacked base `madbot/drop-active-mcp-servers` (ef507ab), **not on develop**; Dewayne's #206 redoes it

- [#189](https://github.com/MadBomber/aia/pull/189) docs: README `--log-level` replaces removed log flags (merged low risk by madbot 2026-11-12 → 22120fa, resolves #186/F-44)
- [#185](https://github.com/MadBomber/aia/pull/185) test: swap VERSION constants without redefinition warnings (merged low risk by madbot 2026-11-12 → 2c1d77b, resolves #179/F-40)
- [#184](https://github.com/MadBomber/aia/pull/184) docs: README aliases for /paste and /available_models (merged low risk by madbot 2026-11-12 → 1b1f227, resolves #180/F-41)
- [#176](https://github.com/MadBomber/aia/pull/176) fix: case-insensitive `state_setting?` (rebase-merged by Dewayne 2026-11-10, resolves #174)
- [#177](https://github.com/MadBomber/aia/pull/177) refactor: share `split_skills` via SkillUtils (rebase-merged by Dewayne 2026-11-10, F-27)
- [#172](https://github.com/MadBomber/aia/pull/172) fix(docs): drop dead mkdocs asset/CDN refs (rebase-merged by Dewayne 2026-11-10, resolves #169)
- [#178](https://github.com/MadBomber/aia/pull/178) docs: fix README's unregistered /shell /next /pipeline directives (merged low risk by madbot 2026-11-10, resolves #175/F-38)
- [#173](https://github.com/MadBomber/aia/pull/173) test: fail the run on an unknown CLI option (merged low risk by madbot 2026-11-10, resolves #165/F-14)
- [#171](https://github.com/MadBomber/aia/pull/171) docs: note /ruby directive needs --allow-ruby-eval (merged low risk by madbot 2026-11-10, resolves #170/F-23)
- [#168](https://github.com/MadBomber/aia/pull/168) test: replace deprecated SimpleCov calls (merged by Dewayne 2026-11-09, resolves #164/F-17)
- [#167](https://github.com/MadBomber/aia/pull/167) fix(skills): recruit and reskill tools accept only skill-directory ids (merged by Dewayne 2026-11-09, security S-2)
- [#166](https://github.com/MadBomber/aia/pull/166) docs: correct README configuration tables to match defaults.yml (merged low risk by madbot 2026-11-10, resolves #163/F-18)
- [#160](https://github.com/MadBomber/aia/pull/160) fix: DelegateHandler and Foreman tool require their classes (merged 2026-11-07)
- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (merged 2026-11-07)
- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (merged 2026-11-07)
- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
