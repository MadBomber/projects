# Findings: robot_lab

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-10 | First run, at main ee0d562 (develop 8ec1d6e is 31 behind; findings checked against it). No lockfile committed; resolved in scratch on 4.1.0-preview1; bundle-audit clean. YAML via safe_load; ScriptTool uses argv + Shellwords; const_get gated to RubyLLM::Tool / RubyLLM::MCP. S-1..S-3 held locally. |
| dead code | 2026-11-12 | Full pass: debride + manual reference check (send/symbol/string) over lib/ at develop 9d407dd, cross-checked main ee0d562 and 16 dependent repos (14 robot_lab-* gems, robot_lab_project, robot_wars). 146 candidates; 41 used only by tests or nowhere, 37 of them public API (left alone). F-46, F-47; no sync-safe PR. |
| dependencies | 2026-11-10 (v2) | First run at develop 83b27c4 / main ee0d562. bundle-audit clean on main's 0.6.0 lockfile; develop's stale `ruby_llm ~> 1.12` resolves to 1.16.0 with 3 High ReDoS CVEs (fixed >= 2.0.0.rc1), resolved by the pending sync (main already on 2.1.1). Ruby floor >= 4.0.9 justified (prompt_manager 1.2.0 requires it). No Dependabot. F-13/14/15. |
| adoption | 2026-11-12 | First run, at main ee0d562 (v0.6.0) / develop 9d407dd, Ruby 4.1.0-preview1. 43 README blocks run in forked processes, no keys: 7 OK, 26 stop at provider config (expected), 10 dependent fragments, 4 real failures (F-24 `param` DSL, also ~60× in docs/; F-25 `tool.call({})`; F-26 `tsort` LoadError on 4.1). CHANGELOG lacks [0.6.0] (F-27), history drift (F-28). `mkdocs build --strict` passes. Examples not run, no LM Studio (F-29). All fixes wait for the sync except F-26 (simple_flow). |
| quality | 2026-11-10 (v3) | First run on the third visit, scoped to the 12 sync-safe lib files: surface clean. F-22/F-23: the Asgard-owned quality gate can't run from a bare clone (needs Dewayne's decision). Reconstructed 2026-11-12: visit 3's brain write was cut off. |

Dead-code scan last covered commit: develop 9d407dd (2026-11-12, full; dependent repos checked; main ee0d562 cross-checked).

## Findings

### 2026-11-10 (first visit; security audit + brief dead-code pass + improver + triage, main ee0d562 / develop 8ec1d6e)

- S-1: security finding, details held locally. Status: needs Dewayne's decision.
- S-2: security finding, details held locally. Status: needs Dewayne's decision.
- S-3: security finding, details held locally. Status: merged [#27](https://github.com/MadBomber/robot_lab/pull/27) (develop e3efd58).
- F-1: `AgentSkillCatalog::SKILLS_ROOT` hardcoded to `~/.prompts/skills` (`lib/robot_lab/agent_skill_catalog.rb:10`), no config key or env var; `Robot` uses the singleton. Proposed: `RobotLab.config.skills_root` defaulting to today's path. Status: needs Dewayne's decision (config surface).
- F-2: stray tracked files: `logfile`, `temp.md` (PR'd [#26](https://github.com/MadBomber/robot_lab/pull/26)); `examples/14_rusty_circuit/show.log`, `examples/16_writers_room/output/opus_00{1,2}_notes.log` (despite `*.log` ignore; examples differ between branches, so wait for the sync); root planning notes `agent2agent_review.md`, `agentf_improvements.md`, `robot_concurrency.md`, `simple_acp_review.md`, `tool_manifest_plan.md` (needs Dewayne's decision: keep, move, delete).
- F-3: dead-code pass found nothing provably dead in `lib/`; all candidates public API. Status: no action.
- F-4: root `agents.md` lists 7 per-directory AGENTS.md files, none exist. Status: merged [#28](https://github.com/MadBomber/robot_lab/pull/28) (low risk, develop 83b27c4).
- F-5: `develop` is 0 ahead / 31 behind `main` (208 files). Status: Dewayne step in `dewayne.md`.
- F-6: no test CI. Status: issue [#30](https://github.com/MadBomber/robot_lab/issues/30).
- F-7: extension gems `robot_lab-{durable,rails,ractor,document_store}` 0.3.0 require `robot_lab ~> 0.3.0`; core is 0.6.0. Status: issue [#29](https://github.com/MadBomber/robot_lab/issues/29).
- F-8: no `llms.txt`. Blocked until the sync: the docs deploy runs on pushes to `develop` touching `docs/**`, so a docs PR merged now republishes develop's stale 0.2.6 docs. Status: open (after sync).
- F-9: gemspec `documentation_uri` is the README, not the docs site. Status: issue [#31](https://github.com/MadBomber/robot_lab/issues/31).
- F-10: docs deploy workflow triggers on both `main` and `develop`, so a docs change merged to a lagging `develop` overwrites the live site. Status: needs Dewayne's decision (deploy from `main` only, or mike versions).
- F-11: `RobotLab::ToolManifest` has no caller in `lib/` (only its test and `tool_manifest_plan.md`). Public API. Status: needs Dewayne's decision (keep for the plan, deprecate, or wire up).
- F-12: `sig/robot_lab.rbs` is the untouched `bundle gem` stub; main's gemspec ships `sig/`. Status: open (low; may be planned).

### 2026-11-10 v2 (second visit; dependencies audit + improver + triage, develop 83b27c4 → 0304617 / main ee0d562)

- F-13: no Dependabot config and no dependency-scan CI; nothing watches the gem's deps. `gh api .../vulnerability-alerts` → 404 (alerts disabled); no `.github/dependabot.yml` on either branch; only workflow is `deploy-github-pages.yml`. Medium (supply-chain blind spot). Proposed: add `.github/dependabot.yml` (bundler + github-actions, weekly) + Dewayne re-enables alerts. Shippable file-wise (absent on both branches) but whether to adopt Dependabot is Dewayne's preference → inbox. Status: needs Dewayne's decision (inbox).
- F-14: develop's stale gemspec pins `ruby_llm "~> 1.12"` → resolves 1.16.0 with 3 High ReDoS CVEs (CVE-2026-67987/67989/67991, fixed >= 2.0.0.rc1). main 0.6.0 already on ruby_llm 2.1.1 and bundle-audit clean. Released gem NOT affected; the pending main→develop sync fixes it. Public CVEs, no local hold needed. Status: resolved by sync (F-5 / dewayne.md).
- F-15: main's gemspec pins `ruby_llm-providers-lms "~> 0.2.1"` (< 0.3.0) while latest is 0.3.0. Low; may be a deliberate API-compat pin. Blocked (gemspec diverges). Status: open (note for Dewayne, possibly intentional).
- F-16: `DelegationFuture#value` (`lib/robot_lab/delegation_future.rb`, identical both branches) guards `@cv.wait` with a single `unless @resolved` and no loop; a spurious wakeup raises a bogus `DelegationTimeout`, and with `timeout: nil` it prints "timed out after s". Verified on main (released). Fix: loop against a monotonic deadline. Not provable with a deterministic test (can't force a spurious wakeup). Status: issue [#33](https://github.com/MadBomber/robot_lab/issues/33).
- F-17: `TextAnalysis.cosine_similarity` (`lib/robot_lab/text_analysis.rb:53,61`, identical both branches, present on main) doc says "clamped to [0.0, 1.0]" but code only clamps the upper bound (`[dot(...), 1.0].min`). Harmless today (word_hash TF vectors are non-negative so dot ≥ 0). Fix direction is ambiguous: code-clamp is speculative robustness (taste says avoid), doc-fix violates "change code not docs". Status: needs Dewayne's decision (inbox).
- F-18: `TextAnalysis.fit` / `TextAnalysis.transform` (`lib/robot_lab/text_analysis.rb:35,45`) appear unused in `lib/` and `examples/` — only their own tests reference the corpus/IDF path; the rest of the repo uses the TF path + `tf_cosine_similarity`. But `RobotLab::TextAnalysis` is documented public API and the sibling `robot_lab-document_store` does semantic scoring, so they may be intended shared API. Removal needs Dewayne's decision + a check of robot_lab-document_store. Status: needs Dewayne's decision (inbox).
- F-19: `DelegationFuture` had no dedicated unit test (only indirect coverage via the branch-divergent `robot_test.rb`). Status: PR'd and merged [#32](https://github.com/MadBomber/robot_lab/pull/32) (low risk, develop 0304617). Added 9 isolated tests for resolve/value/blocking/timeout/reject/wait-alias/readers.
- F-20 (note): `test/test_helper.rb` emits `SimpleCov.add_filter/add_group is deprecated` warnings every run; a swap to `SimpleCov.skip`/`group` would silence them. test_helper differs between branches → after the sync. Status: open (low, after sync).
- F-21 (note): `lib/robot_lab/mcp/transports/streamable_http.rb:50` raises `undefined method 'dig' for nil` (`@session_id ||= result.dig(:serverInfo, :sessionId)`) as an unhandled Async task during the suite; suite still passes. File differs between branches. Status: open (low, after sync; verify it still exists on main's version).

Dead-code scan last covered commit: ee0d562 (main, 2026-11-10; `lib/` only, brief). Not rerun this visit (develop only advanced by the #28/#32 merges, both additive/removal-of-stray; no new lib added).

### 2026-11-10 v3 (third visit; quality audit; reconstructed 2026-11-12 from the uncommitted Status line)

- F-16 update: proven with `Thread#wakeup` and fixed by [#34](https://github.com/MadBomber/robot_lab/pull/34), merged by Dewayne into develop 9d407dd. Issue #33 stays open until develop reaches main (BomberBot noted the fix on #33, 2026-11-12). Status: fixed on develop.
- F-22 / F-23: the Asgard-owned quality gate (rubocop base config, `.loki` tasks) can't run from a bare clone. Status: needs Dewayne's decision (details lost with the cut-off visit; see inbox for the aia equivalent).

### 2026-11-12 (fourth visit; adoption audit + improver + triage, main ee0d562 / develop 9d407dd)

- F-24: README `RubyLLM::Tool` example uses the removed `param ..., desc:` DSL (ruby_llm 2.x has only `parameter ..., description:`) → `NoMethodError`; same form ~60× in 10 `docs/` files. After the sync. Status: issue [#35](https://github.com/MadBomber/robot_lab/issues/35).
- F-25: README `RobotLab::Tool.create` example calls `tool.call({})` → `ArgumentError` (keyword-only `call`). After the sync. Status: issue [#35](https://github.com/MadBomber/robot_lab/issues/35).
- F-26: on Ruby 4.1 `RobotLab::Network` fails to load: simple_flow 0.4.0 requires `tsort` without declaring it, and 4.1 makes tsort a bundled gem. Root fix in simple_flow's gemspec. Status: issue [#36](https://github.com/MadBomber/robot_lab/issues/36).
- F-27: v0.6.0 is tagged and released but CHANGELOG still files its changes under `[Unreleased]`. Status: needs Dewayne's decision (his file; CHANGELOG differs between branches).
- F-28: older CHANGELOG headings drift from tags (0.5.0 body says 0.4.0; no 0.2.7 entry; two `[0.2.1]`). Status: needs Dewayne's decision (low).
- F-29: examples not run end-to-end (need LM Studio at localhost:1234; none running). All parse with `ruby -c`; `examples/README.md` matches. Status: unverified.
- F-30 (note): live docs (gh-pages 34d1695, from main 8cfa143) may predate 0.6.0; `llms.txt` 404 (F-8). Status: open, after sync/F-10.
- F-31: a failed LLM call leaks its budget reservation: `Hooking#run` reserves all remaining budget, and a raise before `reconcile_budget!` never releases it, so a budgeted robot is refused with `BudgetExceeded` two calls later; `reserve_budget!` also strands a partial reservation if a later dimension raises. Reproduced on main 0.6.0. Status: PR'd [#40](https://github.com/MadBomber/robot_lab/pull/40).
- F-40: one SKILL.md with a date (or alias) in front matter raises `Psych::DisallowedClass` from `AgentSkill`, which `AgentSkillCatalog` doesn't rescue, crashing the catalog despite its "skipped with a warning" promise. Status: PR'd [#39](https://github.com/MadBomber/robot_lab/pull/39).
- F-41: a CRLF SKILL.md is rejected as "missing 'name'" (`agent_skill.rb:53` `start_with?("---\n")`). One-line fix. Status: open (small; possibly speculative robustness, so not shipped).
- F-42: `ToolManifest` aliases `count` to `size`, shadowing `Enumerable#count`: block ignored, `count(x)` raises. Status: PR'd [#38](https://github.com/MadBomber/robot_lab/pull/38).
- F-43: `Narrator.enable!` twice registers the hook twice (double narration). Status: issue [#37](https://github.com/MadBomber/robot_lab/issues/37).
- F-44: a budget consumed exactly to its limit isn't refused (`reserve!(:tokens, 0)` passes, `>` not `>=`), though Ledger docs say an exhausted budget is caught. Status: needs Dewayne's decision (boundary semantics).

Dead-code scan: not rerun (develop moved only by #26/#27/#34). Improver found nothing provably dead in the 13 sync-safe lib files.
- F-45 (note): the repo `.rubocop.yml` inherits `../.rubocop-base.yml`, which exists only in `robot_lab_project/`, so RuboCop can't run from a fresh clone (builders copied the base file into the run dir). Same class as F-22/F-23. Status: needs Dewayne's decision.

### 2026-11-12 03:16 (fifth visit; dead-code audit + improver + triage, develop 9d407dd / main ee0d562)

- F-46: `Robot#compact_if_over_context_window` (private, `robot.rb:1056` develop) has no caller since f7fc346 inlined its body into `maybe_compact`; only three `send` tests reach it, so tests cover the dead copy. Fix: one shared helper called from `maybe_compact`, tests retargeted. After the sync (robot.rb, robot_test.rb differ). Status: issue [#42](https://github.com/MadBomber/robot_lab/issues/42).
- F-47: `Robot::TemplateRendering::FRONT_MATTER_CONFIG_KEYS` / `FRONT_MATTER_EXTRA_KEYS` are referenced nowhere (lib, tests, docs, sig, 16 dependent repos); the first duplicates `RunConfig::LLM_FIELDS`. `from_front_matter` also hardcodes `%i[mcp tools]` instead of `RunConfig::TOOL_FIELDS`. Status: needs Dewayne's decision (public constants: delete, or alias to `RunConfig::LLM_FIELDS`).
- F-48: `test/test_helper.rb:26` `require 'robot_lab/document_store' rescue LoadError` never rescues (modifier catches StandardError only), so the suite dies without the extension gem. Fix: begin/rescue block, after the sync (test_helper differs). Status: issue [#41](https://github.com/MadBomber/robot_lab/issues/41).
- F-49: `convergence.rb:4,26` header says "TF-IDF" but the method doc (`:46-53`) and code use TF only (`tf_cosine_similarity`); `text_analysis.rb:4` similar. Comment-only, sync-safe. Status: open (low-risk PR candidate next visit).
- F-50: `MCPError` and `ToolError` in `error.rb` duplicate the `retryable` reader + initializer (~8 lines). Status: open (low; tidy-up only).
- F-51 (note): `asgard test` fails with `NameError: undefined local variable or method 'gem_version'` at `.loki:9` (tender). Same class as F-22/F-23/F-45. Status: needs Dewayne's decision (Asgard-owned).
- Notes: `Memory#create_semantic_cache` and develop's `sandbox*.rb` look dead on develop only because develop trails main; the sync resolves them. robot_lab-sandbox pins `robot_lab ~> 0.3.0` (same as #29). Root design notes (`agent2agent_review.md`, `agentf_improvements.md`, `simple_acp_review.md` with a local path, `tool_manifest_plan.md`, `robot_concurrency.md`) → inbox. Stale branches `free_will`, `refactor_config`, `rethink_network`, `update-rubyllm-v2.0.0` have nothing not on main (triage).

