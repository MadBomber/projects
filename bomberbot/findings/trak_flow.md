# Findings: trak_flow

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-09 | First run (first visit), at main 3fa6fb4 (no `develop` branch). No eval/Marshal/YAML.load/string-SQL in lib; Oj `:compat`, `YAML.safe_load_file`, escaped LIKE, `Open3` array-form `dot`. Found S-1 (MCP HTTP binds 0.0.0.0, no auth — needs decision) and S-2 (git shell-out interpolates a config-derived path — clean fix). Both held locally. |
| dead code | 2026-11-09 (lib only, brief) | debride over lib/bin/test/docs/examples at main 3fa6fb4. No removable dead code: public-API orphans only (F-1, F-2). |
| dependencies | n/a | Not yet run. Triage noted: Dependabot alerts appear disabled (`vulnerability-alerts` → 404); no `.github/dependabot.yml`; safe minors available (anyway_config 2.9.1, sequel 5.110, sqlite3 2.10.2, oj 3.17.8, rack 3.2.8, rubocop 1.92.2, reek 6.6.0); majors to defer: ruby_llm 2.x, json 3, minitest 6, simplecov 1.x, marcel 2, unicode-display_width 3. |
| adoption | n/a | Not yet run. Partly covered: #3 (gemspec metadata URIs), C4 (README omits `tf tree`/`tf version`), no `llms.txt`, docs vs gemspec Ruby-floor contradiction (3.1 vs 3.2). |
| quality | n/a | Not yet run. Suite green (354 runs, 0 failures, 88.85% line) on Ruby 4.0.9. No test CI. |

Dead-code scan last covered commit: 3fa6fb4 (main, 2026-11-09; no develop branch exists)

## Findings

### 2026-11-09 (first visit; security audit + brief dead-code pass, main 3fa6fb4)

- S-1: security finding, details held locally. Status: needs Dewayne's decision (default bind/auth is a design choice). Also blocked (no `develop`).
- S-2: security finding, details held locally. Status: open — clean array-form fix, would be an obvious-yes PR once `develop` exists. Not a public issue (security).
- F-1: `DependencyGraph` public methods with no callers in the repo (`all_blockers`, `all_blocked`, `critical_path`, `leaf_tasks`, `root_tasks`, `graph/dependency_graph.rb`). Sibling methods (`to_dot`/`to_svg`/`analyze`/`dependency_tree`/`blocking_dependencies`) are used, so the class is alive; these five are public API, not removable dead code. Status: needs Dewayne's decision (keep as API + add coverage, or remove). Verified (no callers in lib/bin/test/docs/examples).
- F-2: `Jsonl#incremental_export` and `Jsonl#changed_since?` (`storage/jsonl.rb`) have no callers; `incremental_export` looks like an intended sync optimization never wired in (`export` is used instead). Public storage API, so not auto-removable. Status: needs Dewayne's decision (wire in or drop both). Verified (no callers).
- F-3: `validation.on_create` / `validation.on_sync` config knobs (`config/defaults.yml`, accessors `config.rb` `validation_on_create`/`validation_on_sync`) are declared and documented ("Access: TrakFlow.config.validation.on_create") but never read anywhere in lib/bin — setting them has no effect. Status: open, verified (grep finds only the accessor defs + doc comment). Fix is a decision: enforce at create/sync time, or remove the no-op knobs and their doc promise (prefer fixing code over docs per taste). May be intentionally staged WIP → inbox.
- C1 (improver, adoption): gemspec sets only `rubygems_mfa_required`; no `documentation_uri`/`source_code_uri`/`changelog_uri` despite a live docs site + CHANGELOG. Status: issue [#3](https://github.com/MadBomber/trak_flow/issues/3).
- C2 (improver): MCP `start_http`/`start_both` write the startup banner with `puts` (stdout) while `start_stdio` uses `warn`; in `--both` the banner corrupts the stdio JSON-RPC stream. Status: issue [#1](https://github.com/MadBomber/trak_flow/issues/1).
- C3 (improver): `examples/cli_demo.sh` exports PATH/BUNDLE_GEMFILE but not a DB path, so the advertised demo reads/writes the user's real global `~/.config/trak_flow/tf.db`. Status: issue [#2](https://github.com/MadBomber/trak_flow/issues/2).
- C4 (improver): README "Core Commands" table omits the real commands `tf tree` and `tf version` (`tf --help` lists both). Status: open, verified (small docs PR once `develop` exists).
- C5 (improver): byte-identical `output(json_data)` helpers duplicated across six CLI classes (plus duplicated `pastel`); extract a mixin. Status: open (refactor; wide diff → inbox I3).
- DATABASE_FILE (improver): top-level public constant `TrakFlow::DATABASE_FILE = "trak_flow.db"` (`lib/trak_flow.rb`) is unused and contradicts the real default db filename `tf.db`; sibling `JSONL_FILE` is still used. Status: needs Dewayne's decision (remove as dead/misleading, or use it if the DB goes project-local). Tied to the global-vs-local DB inbox item.
- Triage adoption: docs say "Ruby 3.2 or later" (`docs/getting-started/installation.md`) while the gemspec says `>= 3.1.0` — a contradiction. Status: needs Dewayne's decision (align docs to 3.1, or raise the floor to 3.2). → inbox.
