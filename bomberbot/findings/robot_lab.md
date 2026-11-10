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
| dependencies | n/a | Not yet run. Triage: extension gems 0.3.0 pin `robot_lab ~> 0.3.0` (F-7, issue #29); `bundle install` fails on 4.1.0-preview1 (tokenizers/rb-sys via robot_lab-document_store); Dependabot alerts disabled. |
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
