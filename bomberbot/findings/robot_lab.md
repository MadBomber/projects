# Findings: robot_lab

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-10 | First run, at main ee0d562 (develop 8ec1d6e is 31 behind; findings checked against it). No lockfile committed; resolved in scratch on 4.1.0-preview1; bundle-audit clean. YAML via safe_load; ScriptTool uses argv + Shellwords; const_get gated to RubyLLM::Tool / RubyLLM::MCP. S-1..S-3 held locally. |
| dead code | 2026-11-10 (lib only, brief) | debride over `lib/` at main ee0d562: ~120 candidates, all public API or extension hooks; nothing provably dead. Dependent extension gems not checked. |
| dependencies | 2026-11-10 (v2) | First run at develop 83b27c4 / main ee0d562. bundle-audit clean on main's 0.6.0 lockfile; develop's stale `ruby_llm ~> 1.12` resolves to 1.16.0 with 3 High ReDoS CVEs (fixed >= 2.0.0.rc1), resolved by the pending sync (main already on 2.1.1). Ruby floor >= 4.0.9 justified (prompt_manager 1.2.0 requires it). No Dependabot. F-13/14/15. |
| adoption | n/a | Not yet run. No `llms.txt` (F-8); `documentation_uri` points at README (F-9, issue #31). |
| quality | n/a | Not yet run. Gemfile has flog/flay/reek/rubocop/fasterer; quality gate is Asgard-owned. |

Dead-code scan last covered commit: ee0d562 (main, 2026-11-10; `lib/` only, brief). develop 8ec1d6e not scanned separately.

## Findings

### 2026-11-10 (first visit; security audit + brief dead-code pass + improver + triage, main ee0d562 / develop 8ec1d6e)

- S-1: security finding, details held locally. Status: needs Dewayne's decision.
- S-2: security finding, details held locally. Status: needs Dewayne's decision.
- S-3: security finding, details held locally. Status: PR'd [#27](https://github.com/MadBomber/robot_lab/pull/27).
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
