# trak_flow

Dependency-graph-based task tracker "for Robots": a Thor CLI (`tf`) plus an MCP server (`tf_mcp`, via fast-mcp) over the same task store. Tasks are kept as JSONL (git-versioned) with a SQLite/Sequel working copy; a dependency graph schedules and visualizes work (`dot` → SVG). MCP exposes tools (task/dep/plan/label/workflow) and resources. Released 0.2.0 (2026-09-08). Ruby floor `>= 3.1.0`.

## Links

- GitHub: https://github.com/MadBomber/trak_flow
- RubyGems: https://rubygems.org/gems/trak_flow
- Docs: https://madbomber.github.io/trak_flow/
- Issues: https://github.com/MadBomber/trak_flow/issues

## Status

_Updated 2026-11-09 (first visit): **blocked for PRs — no `develop` branch** (only `main` + `gh-pages`), same rule as typed_bus/agent99. No PRs, no issues existed. Ran the first-ever security audit (+ brief dead-code pass) and filed three issues (#1–#3) for ready fixes. Suite green on Ruby 4.0.9 (354 tests, 710 assertions, 0 failures, 88.85% line coverage). No test CI exists (only a docs-deploy workflow). `bundle exec` fails under Ruby 4.1.0-preview1 (broken bundler), as elsewhere._

## Backlog

Well-defined items a single small PR could close (all blocked until a `develop` branch exists).

- [#1](https://github.com/MadBomber/trak_flow/issues/1) MCP `--both`/`--http` startup banner goes to stdout, corrupting the stdio protocol stream (C2).
- [#2](https://github.com/MadBomber/trak_flow/issues/2) `examples/cli_demo.sh` mutates the user's real global task DB (C3).
- [#3](https://github.com/MadBomber/trak_flow/issues/3) gemspec is missing metadata URIs — docs site, source, changelog (C1).
- S-2: task-sync `git` step interpolates a config-derived path into a shell command (security, held locally; clean array-form fix once `develop` exists).
- F-3: `validation.on_create`/`on_sync` config knobs are declared and documented but never read (enforce or remove — needs a decision).
- README "Core Commands" table omits `tf tree` and `tf version` (C4).
- Add a test-CI workflow (the 354-test suite runs nowhere); add `.github/dependabot.yml` (alerts appear disabled).

## Open bomberbot PRs

- (none — blocked, no `develop` branch)

## Merged bomberbot PRs

- (none yet)
