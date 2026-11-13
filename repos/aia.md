# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-13 (08:48 visit)._ **Skipped new PRs: the review queue is full.** #226–#229 and #233 are unchanged and unapproved, and develop is still 0d611ef. The quality audit and the improver found three issues. Log lines default to stdout and mix into piped answers ([#240](https://github.com/MadBomber/aia/issues/240), F-144). Malformed `--mcp` JSON files fail badly ([#241](https://github.com/MadBomber/aia/issues/241), F-145). `TurnState` has a duplicated clear method ([#242](https://github.com/MadBomber/aia/issues/242), F-141).

_Prior (2026-11-13 08:00 visit)._ **Skipped new PRs: the review queue is full.** #226–#229 and #233 are unchanged and unapproved; develop is still 0d611ef and none of Dewayne's #206–#222 has merged. The dependencies audit found that the unit suite opens the user's real TrakFlow database ([#237](https://github.com/MadBomber/aia/issues/237), F-135; land before the #236 fix). Also filed [#238](https://github.com/MadBomber/aia/issues/238) (`--role` never validated, F-136) and [#239](https://github.com/MadBomber/aia/issues/239) (`/tools` with a description-less tool, F-137).

_Prior (2026-11-13 02:55 visit)._ **Skipped new PRs: the review queue is full.** #226–#229 and #233 are unchanged, unapproved and 0 behind develop, and develop is still 0d611ef. The adoption audit found that TrakFlow fails on a fresh install ([#236](https://github.com/MadBomber/aia/issues/236), F-116) and that the live docs site was rolled back to 2.0.0 by a develop deploy (F-119, needs Dewayne). Also filed [#234](https://github.com/MadBomber/aia/issues/234) (F-130) and [#235](https://github.com/MadBomber/aia/issues/235) (F-131).

_Prior (2026-11-13 02:05 visit)._ Nothing approved or merged; develop still 0d611ef. Bomberbot opened [#228](https://github.com/MadBomber/aia/pull/228) (F-69, #223), [#229](https://github.com/MadBomber/aia/pull/229) (F-103) and [#233](https://github.com/MadBomber/aia/pull/233) (F-101, #224); five bomberbot PRs now await review, so the next visit will skip new work. Filed #230 (F-111), #231 (F-112), #232 (F-62).

_Prior (2026-11-13 00:15 visit)._ Dewayne rebase-merged #183 and #188 into develop (now 0d611ef); #193 merged only into its stacked base, and his own #206 redoes it. He also opened 17 fix PRs, #206–#222, covering nearly every open issue. Bomberbot opened [#226](https://github.com/MadBomber/aia/pull/226) (F-68) and [#227](https://github.com/MadBomber/aia/pull/227) (F-88 removal). A test-CI workflow is built but blocked: the bots' gh token lacks the `workflow` scope (`bomberbot/plans/aia-test-ci.md`). Filed #223 (F-69), #224 (F-101) and #225 (F-50).

_Prior (2026-11-12 08:45 visit)._ **Skipped new PRs a fourth time: the review queue is full** (#183, #188, #193 unchanged, unapproved; develop still 22120fa). The improver found that **every stdio MCP server fails to start under ruby_llm 2.2** ([#203](https://github.com/MadBomber/aia/issues/203), F-100; also S-5, held locally, which constrains the fix). Also filed [#204](https://github.com/MadBomber/aia/issues/204) (`/config`, F-64+F-82) and [#205](https://github.com/MadBomber/aia/issues/205) (README model IDs and Quick Start key step, F-102). The dead-code audit found F-86..F-90.

_Prior (2026-11-12 07:30 visit)._ **Skipped new PRs a third time: the review queue is full.** #183, #188 and #193 are unchanged and unapproved, and develop is still 22120fa. The quality audit and the improver found that **cost and token metrics have been broken since the move to ruby_llm 2**: filed [#200](https://github.com/MadBomber/aia/issues/200) (F-80, prices) and [#201](https://github.com/MadBomber/aia/issues/201) (F-81, tokens). Also filed [#202](https://github.com/MadBomber/aia/issues/202) (a failing directive ends the chat, F-65). F-67 (1.x log truncated on upgrade) needs Dewayne.

_Prior (2026-11-12 06:16 visit)._ **Skipped new PRs again: the review queue is full.** #183, #188 and #193 are unchanged, MERGEABLE and unapproved, and develop is still 22120fa. Filed [#197](https://github.com/MadBomber/aia/issues/197) (zsh/fish completion broken, F-61), [#198](https://github.com/MadBomber/aia/issues/198) (`debug_me` runtime dep, F-58) and [#199](https://github.com/MadBomber/aia/issues/199) (`mcp_servers/README.md` drift, F-63). The dependencies audit is clean apart from F-58..F-60; F-59 (tty-table breaks under frozen string literals) needs Dewayne.

_Prior (2026-11-12 03:44 visit)._ **Skipped new PRs: the review queue is full.** The tender rebased #183, #188 and #193 onto 22120fa (all CLEAN, suites green), and none is approved yet. Filed [#194](https://github.com/MadBomber/aia/issues/194) (`--provider` ignored for bare slug names; F-19 was never actually merged, and its fix is stranded on `bomber/provider-bare-model-name`), [#195](https://github.com/MadBomber/aia/issues/195) (`--fuzzy` broken) and [#196](https://github.com/MadBomber/aia/issues/196) (`--require` spaces). The adoption audit found F-49..F-53 and S-3.


## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- **In flight (Dewayne's PRs #206–#222)** cover #154, #182, #190–#192 and #194–#205. Don't build anything touching their files until they merge; afterwards F-49, F-50 (#225), F-83, F-28 and F-31 unblock.
- **Now in PRs:** F-69 ([#223](https://github.com/MadBomber/aia/issues/223)) → **PR open** [#228](https://github.com/MadBomber/aia/pull/228); F-101 ([#224](https://github.com/MadBomber/aia/issues/224)) → **PR open** [#233](https://github.com/MadBomber/aia/pull/233); F-103 → **PR open** [#229](https://github.com/MadBomber/aia/pull/229).
- **New picks (2026-11-13 08:48):** F-144 ([#240](https://github.com/MadBomber/aia/issues/240), `defaults.yml` + `logger.rb` STDOUT→STDERR, about 6 lines plus a logger test; no overlap; user-facing top pick); F-145 ([#241](https://github.com/MadBomber/aia/issues/241), `mcp_parser.rb`, stack on or follow #227); F-141 ([#242](https://github.com/MadBomber/aia/issues/242), after #222).
- **New picks, no overlap with open PRs (2026-11-13 08:00):** F-135 ([#237](https://github.com/MadBomber/aia/issues/237), test-only, six stubs in `trakflow_bridge_test.rb`; proven in scratch; build first or stack F-116 on it); F-136 ([#238](https://github.com/MadBomber/aia/issues/238), `validator.rb` `process_role_configuration`, about 3 lines plus 2 tests); F-137 ([#239](https://github.com/MadBomber/aia/issues/239), `utility_directives.rb:119`, about 3 lines plus 1 test); F-138 (`speech.rb`, about 5 lines). After #213: F-139 (bash completion `cd`). F-140 (`/compare` provider) needs a small builder decision about local providers. Unfiled-but-ready: F-117 (EBADF, after #208) and F-120 (README blog 404s).
- **Picks from 2026-11-13 02:55:** F-116 ([#236](https://github.com/MadBomber/aia/issues/236), `task_board.rb`, about 1 line plus a temp-HOME test; top pick); F-130 ([#234](https://github.com/MadBomber/aia/issues/234), `model_directives.rb:190`, 1 line; proof test in the improver report); F-131 ([#235](https://github.com/MadBomber/aia/issues/235), `plugin_loader.rb`, about 8 lines plus 2 tests; pair F-133 after it); F-132 (`max_tools` removal, about -4). After their overlapping PRs merge: F-117 (after #208) and F-120 (README blog 404s, after #207/#221/#222).
- **Next picks with no overlap:** F-104 after #226 merges (same file; would also make #228's Hash guard redundant); F-62 ([#232](https://github.com/MadBomber/aia/issues/232), Rakefile, −6); F-71; F-36; the docs half of #205 (`docs/prompts/executable-prompts.md:63`, `docs/prompts/directives.md:91` use `claude-sonnet-4`, which resolves to vertexai; docs-deploy so not low risk); `lib/AGENTS.md` refresh (F-57, not shipped in the gem); the test CI workflow once the token has `workflow` scope.
- **After their overlapping PR merges:** F-111 ([#230](https://github.com/MadBomber/aia/issues/230), after #208), F-112 ([#231](https://github.com/MadBomber/aia/issues/231), after #210), F-108 (after #206/#217/#218).
- Open issues a PR can resolve (most now have a PR from Dewayne):
  - [#203](https://github.com/MadBomber/aia/issues/203): stdio MCP servers can't start under ruby_llm 2.2 (F-100, lib, about 10 lines plus a lambda-`env:` test). **Top pick**: a headline feature is broken in the released gems. The builder must read S-5 (local notes) before choosing the fix. F-103 (silent MCP failures off-TTY) is in **PR open** [#229](https://github.com/MadBomber/aia/pull/229).
  - [#204](https://github.com/MadBomber/aia/issues/204): `/config` strips `=` and shows `nil` for nested keys (F-64+F-82, lib, about 15 lines plus tests).
  - [#205](https://github.com/MadBomber/aia/issues/205): README model IDs unknown to the registry, no API-key step in Quick Start (F-102; README half is a low-risk candidate, `docs/` half is docs-deploy).
  - [#194](https://github.com/MadBomber/aia/issues/194): `--provider ollama -m mistral` goes to `mistral/default` (F-19, lib, user-facing). The fix and tests are on `origin/bomber/provider-bare-model-name` 9a58ed1; cherry-pick the lib/test hunks onto develop (only CHANGELOG conflicts). Top pick when the queue has room.
  - [#195](https://github.com/MadBomber/aia/issues/195): `--fuzzy` can't start fzf, and a cancel prints a backtrace (F-54, lib + test).
  - [#196](https://github.com/MadBomber/aia/issues/196): `--require` keeps spaces; a repeated `--tools` keeps only the last value (F-55/F-56, lib, tiny).
  - F-50 (README `just` commands → rake/mkdocs) and F-49 (README config reference gaps): docs-only, low-risk candidates, not filed yet.
  - [#190](https://github.com/MadBomber/aia/issues/190): README tool/MCP examples point at deleted files (F-47, docs-only, low-risk candidate).
  - [#192](https://github.com/MadBomber/aia/issues/192): `utility_test` prints `method redefined` warnings (F-48, test-only, low-risk candidate). #193 also edits `test/aia/utility_test.rb`, so build it after #193 merges or stack it on #193.
  - [#197](https://github.com/MadBomber/aia/issues/197): zsh and fish completion scripts never complete prompt IDs (F-61, lib shell scripts; the fixes were checked in improver scratch). User-facing, about 35 lines plus a skip-if-missing test.
  - [#198](https://github.com/MadBomber/aia/issues/198): `debug_me` is required at runtime but dev-only in the gemspec (F-58, one line, gemspec so never low risk; check the merge against `main` first).
  - [#199](https://github.com/MadBomber/aia/issues/199): `mcp_servers/README.md` names a deleted `imcp.json`, covers 3 of 7 files, has dead links (F-63, docs/examples, low-risk candidate).
  - F-62: dead kramdown-man block in `Rakefile:10-15` warns on every rake run (removal, build tooling so not low risk; not filed).
  - [#191](https://github.com/MadBomber/aia/issues/191): `--tools` files load twice and their warnings print twice (F-33, lib).
- [#202](https://github.com/MadBomber/aia/issues/202): a directive that raises ends the chat session (F-65, lib, about 4 lines plus a test). Small and user-facing; top pick next to #194.
- [#200](https://github.com/MadBomber/aia/issues/200): `--cost`, `/cost` and `/robots` never show a price under ruby_llm 2 (F-80, lib, about 15 lines plus a test against the bundled `models.json`). User-facing.
- [#201](https://github.com/MadBomber/aia/issues/201): `--tokens` shows nothing, and turns record 0 tokens (F-81, lib, about 60 lines including test doubles). Build after #200; it touches `pipeline_orchestrator.rb` (hold rule).
- F-68 then F-69: skill/role front matter with a Date is dropped; `/skills` has a third parser that cuts at `---` (1 line, then about −10). Not filed.
- F-83: `docs/reference/directives.md` says `/temperature`/`/top_p` don't retune the running robot (2 lines, docs). Not filed.
- F-88 (MCP `topics` metadata parsed then dropped, about −2 lib lines, no overlaps) and F-87 (write-only `decisions:` keyword, about −10; build after #193, pair with F-72). F-101: missing-API-key message shows Ruby code and wrong Vertex env names (about 25 lines). Not filed.
- F-71 (ignored `ephemeral:` keyword, about −4) and F-84 (`FactAsserter` removal, about −27): removals, lib. Not filed.
- Already in PRs: #187 → **PR open** [#188](https://github.com/MadBomber/aia/pull/188); #182 → **PR open** [#193](https://github.com/MadBomber/aia/pull/193) (stacked on #188); #181 → **PR open** [#183](https://github.com/MadBomber/aia/pull/183).
- Quality DRY refactors still un-filed, all re-verified on 2c1d77b by triage:
  - F-28: config `reset_section`/`merge_into_section` (`config.rb:363-383`).
  - F-31: output-append idiom repeated in 5 places. `content_extractor` writes `"\nAI:"` but `pipeline_orchestrator` writes `"AI:"`, so keep the strings as they are.
  - F-36: network_builder setup.
  - F-32 has a taste caveat.
  Do them one at a time, because sibling CHANGELOG `[Unreleased]` lines conflict on rebase.
- **Blocked on Dewayne:** F-45/F-46, the main→develop sync (inbox). Hold any `pipeline_orchestrator.rb` PR until it is decided.
- `PromptHandler::SHORTHAND_KEYS` (part of F-13) is verified test-only. Removing it changes a public constant, so it belongs in the F-13 batch decision (inbox).
- F-5: redundant per-file `require 'ostruct'`/`require_relative` lines in test files. Low value.
- Builder notes: aia has no `exe/`; the executable is `bin/aia`. `.rubocop.yml` inherits `../.rubocop-base.yml`, which is not in the repo; builders copied one to `~/sandbox/bomberbot/aia/build/`. The `src/` clone checks out `main`, so always base work on `origin/develop` with absolute worktree paths.

## Open bomberbot PRs

- [#226](https://github.com/MadBomber/aia/pull/226) fix: permit dates in skill and role front matter (F-68). **PR open**; review clean in 2 rounds, suite 2003/0. Lib, not low risk.
- [#227](https://github.com/MadBomber/aia/pull/227) refactor: drop unused MCP `topics` metadata (F-88, part of F-90). **PR open**; review clean in 2 rounds, suite 2001/0. Lib deletion, not low risk.
- [#228](https://github.com/MadBomber/aia/pull/228) fix(skills): use shared front-matter parser in /skills (F-69, Fixes #223). **PR open**; review clean in 1 round, suite 2004/0. Lib, not low risk.
- [#229](https://github.com/MadBomber/aia/pull/229) warn about failed MCP servers when stderr is not a terminal (F-103). **PR open**; review clean in 2 rounds, suite 2004/0. Lib, not low risk.
- [#233](https://github.com/MadBomber/aia/pull/233) fix: name the env var to set when a provider API key is missing (F-101, Fixes #224). **PR open**; review clean in 3 rounds, suite 2005/0. Lib, not low risk.
- Tender (2026-11-13 08:00): all five unchanged, MERGEABLE, 0 behind develop, no reviews or comments; nothing rebased. The repo has no test CI on PRs, so "green" means no conflicts.
- Tender (2026-11-13 02:55): all five are unchanged, MERGEABLE, 0 behind develop, suites green locally (2001–2005/0), no reviews. A scratch merge showed that once #226 and #227 land, #228, #229 and #233 conflict on CHANGELOG only.
- Tender (2026-11-13 02:05): #226 and #227 up to date with develop, suites green, unreviewed. Each will need a CHANGELOG-only rebase against whichever of Dewayne's #206–#222 lands first.


## Merged bomberbot PRs

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
