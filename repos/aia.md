# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-12 (07:30 visit)._ **Skipped new PRs a third time: the review queue is full.** #183, #188 and #193 are unchanged and unapproved, and develop is still 22120fa. The quality audit and the improver found that **cost and token metrics have been broken since the move to ruby_llm 2**: filed [#200](https://github.com/MadBomber/aia/issues/200) (F-80, prices) and [#201](https://github.com/MadBomber/aia/issues/201) (F-81, tokens). Also filed [#202](https://github.com/MadBomber/aia/issues/202) (a failing directive ends the chat, F-65). F-67 (1.x log truncated on upgrade) needs Dewayne.

_Prior (2026-11-12 06:16 visit)._ **Skipped new PRs again: the review queue is full.** #183, #188 and #193 are unchanged, MERGEABLE and unapproved, and develop is still 22120fa. Filed [#197](https://github.com/MadBomber/aia/issues/197) (zsh/fish completion broken, F-61), [#198](https://github.com/MadBomber/aia/issues/198) (`debug_me` runtime dep, F-58) and [#199](https://github.com/MadBomber/aia/issues/199) (`mcp_servers/README.md` drift, F-63). The dependencies audit is clean apart from F-58..F-60; F-59 (tty-table breaks under frozen string literals) needs Dewayne.

_Prior (2026-11-12 03:44 visit)._ **Skipped new PRs: the review queue is full.** The tender rebased #183, #188 and #193 onto 22120fa (all CLEAN, suites green), and none is approved yet. Filed [#194](https://github.com/MadBomber/aia/issues/194) (`--provider` ignored for bare slug names; F-19 was never actually merged, and its fix is stranded on `bomber/provider-bare-model-name`), [#195](https://github.com/MadBomber/aia/issues/195) (`--fuzzy` broken) and [#196](https://github.com/MadBomber/aia/issues/196) (`--require` spaces). The adoption audit found F-49..F-53 and S-3.


## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- Open issues a PR can resolve:
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
- F-64 + F-82: `/config` strips `=` from values and shows `nil` for nested keys (same method, `configuration_directives.rb`). One small lib PR; not filed.
- F-68 then F-69: skill/role front matter with a Date is dropped; `/skills` has a third parser that cuts at `---` (1 line, then about −10). Not filed.
- F-83: `docs/reference/directives.md` says `/temperature`/`/top_p` don't retune the running robot (2 lines, docs). Not filed.
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

- [#183](https://github.com/MadBomber/aia/pull/183) refactor(spawn): reuse `Crew.model_opts` in SpawnHandler (Fixes #181/F-29). **PR open**, awaiting Dewayne. The tender rebased it onto 2c1d77b (suite 2003/0).
- [#188](https://github.com/MadBomber/aia/pull/188) refactor: remove write-only `TurnState#active_mcp_servers` (Fixes #187/F-43). **PR open**. It touches lib, so it is not low risk. Review clean in 1 round, suite 2001/0.
- [#193](https://github.com/MadBomber/aia/pull/193) refactor: share one model-id lookup (`AIA::Utility.model_id_of`) (Fixes #182/F-30). **PR open**, **stacks on #188**. Review clean in 1 round, suite 2002/0. Retarget to develop once #188 merges.

## Merged bomberbot PRs

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
