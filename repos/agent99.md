# agent99

A Ruby framework (v0.0.5) for building and managing intelligent software agents in a
distributed system: agent discovery, peer-to-peer messaging (AMQP via bunny, NATS via
nats-pure, and an experimental TCP client), a centralized Sinatra registry, lifecycle
and control actions, and JSON message processing. The README cautions: "Initial release
has no AI components" — it's a generic client-server / request-response micro-services
system today. Named for Agent 99 from *Get Smart*.

## Links

- GitHub: https://github.com/MadBomber/agent99
- RubyGems: https://rubygems.org/gems/agent99
- Docs: https://madbomber.github.io/agent99/
- Issues: https://github.com/MadBomber/agent99/issues

## Status

_Updated 2026-11-13 08:25 (fifteenth visit)._ Still nothing merged or approved (develop 28a6347); #55–#57 open, so no PR was opened. Adoption delta + improver found F-119..F-124; filed #73–#75.


## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- **In flight in Dewayne's own PRs (don't overlap until they land):** #7 → #53, #14 → #38, #16 → #43, #18 → #41, #19 → #42, #22 → #44, #24 → #45, #25 → #46, #26 → #54, #30 → #47, #31 → #48, #32 → #49, #33 → #52, #34 → #50, #35 → #40, #36 → #39, #37 → #51. Off limits while open: `message_processing.rb`, `header_management.rb`, `base.rb`, `agent_discovery.rb`, `agent_lifecycle.rb`, `examples/control.rb`, `examples/maxwell_agent86.rb`, README, `docs/getting-started/{basic-example,quick-start}.md`, gemspec, `Gemfile.lock`, `bin/console`, `bin/test`, `Rakefile`, `.github/workflows/test.yml`, `test/README.md`, and the test files they edit. 16 of them add their CHANGELOG line at the end of `[Unreleased]` → Fixed and conflict pairwise there; madbot PRs insert higher up to stay clear.
- #3, #4, #5, #6, #8, #15, #20, #21, #23 and F-2: fixed on `develop` (#9–#13, #17, #27–#29). Issues stay open until develop reaches `main`.
- F-55: remote shutdown runs `fini` twice + false warning. **PR open** [#55](https://github.com/MadBomber/agent99/pull/55) Since 2026-11-13 it also covers F-104 (a NATS agent kept running after a remote shutdown).
- F-65 + F-71: `RegistryClient` drops the URL path and ignores the scheme. **PR open** [#56](https://github.com/MadBomber/agent99/pull/56).
- F-59 (part): stray notes after `__END__` in `tcp_message_client.rb` / `header_schema.rb`. **PR open** [#57](https://github.com/MadBomber/agent99/pull/57).
- [#58](https://github.com/MadBomber/agent99/issues/58) F-90: fixed `sleep 0.5` per test makes the suite 38 s instead of ~1.5 s (tests-only, 2 lines; low-risk candidate once #54 lands).
- [#59](https://github.com/MadBomber/agent99/issues/59) F-77: docs show 0.0.4 as current, 0.0.5 missing (docs-only, ~4 lines; low-risk candidate).
- [#60](https://github.com/MadBomber/agent99/issues/60) F-92: control handlers untested (one new test file; after #55, which creates `test_control_actions.rb`).
- [#61](https://github.com/MadBomber/agent99/issues/61) F-100: private `info`/`init` ignored at startup (lib 2 lines + tests in `test_agent_lifecycle.rb`; after #42, same lines).
- [#62](https://github.com/MadBomber/agent99/issues/62) F-101: custom control action docs snippet raises NameError (docs-only, ~3 lines; low-risk candidate, no open PR touches the file).
- [#63](https://github.com/MadBomber/agent99/issues/63) F-93: `bin/console` can't load irb on Ruby 4.0+ (Gemfile + lock; after #53).
- S-17: security finding, details held locally; one targeted conservative re-lock after #53 (with S-12).
- F-94 (lock needs Ruby >= 4.0 after #53, ties to F-68) and F-102 (pause drops requests vs docs): **needs Dewayne's decision**.
- [#64](https://github.com/MadBomber/agent99/issues/64) F-105: docs say `REGISTRY_BASE_URL`, the code reads `AGENT99_REGISTRY_URL` (docs-only, 5 lines in 4 files; low-risk candidate, no open PR touches them).
- [#65](https://github.com/MadBomber/agent99/issues/65) F-106: `run_demo.rb` generates agents with `require_relative '../lib/agent99'` (examples, 1 line).
- [#66](https://github.com/MadBomber/agent99/issues/66) F-108: `docs/agent99_framework/` wrong signatures for `register`/`publish`/`delete_queue`/`initialize` (docs-only, ~8 lines; low-risk candidate; can ride with #64).
- [#67](https://github.com/MadBomber/agent99/issues/67) F-109: docs Ruby samples that don't parse (registry irb walkthrough) and `on_exit` → `at_exit` (docs-only, ~15 lines in 4 files; low-risk candidate, no open PR touches them).
- [#68](https://github.com/MadBomber/agent99/issues/68) F-110: misspellings in docs/examples incl. `RegristryClient`, `@agnet_*` (docs-only; low-risk candidate; can ride with #67).
- [#69](https://github.com/MadBomber/agent99/issues/69) F-111: `info` without `name` exits though docs say it is derived; default to class name + test (lib, after #42, with #61).
- [#70](https://github.com/MadBomber/agent99/issues/70) F-117: `examples/maxwell_request.rb` raises NameError on its own (examples, 1 line; no open PR touches it).
- [#71](https://github.com/MadBomber/agent99/issues/71) F-114: most AMQP client unit tests exercise the mock (tests-only, one file; no open PR touches it).
- [#72](https://github.com/MadBomber/agent99/issues/72) F-116: `NatsMessageClient` publish/dispatch/delete_queue untested (tests-only, one file).
- [#73](https://github.com/MadBomber/agent99/issues/73) F-119: tutorial `curl` commands hit registry routes that 404 (`basic-example.md:263,266`, `basic-examples.md:258,261`; docs-only, 4 lines; low-risk candidate; `basic-example.md` is also in #45, different lines).
- [#74](https://github.com/MadBomber/agent99/issues/74) F-122: dead `docs/README.md` link in `what-is-an-agent.md:25` (docs-only, 1 line; low-risk candidate; can ride with #73).
- [#75](https://github.com/MadBomber/agent99/issues/75) F-124: unknown control action gets no reply (`message_processing.rb:89-93`, 1 lib line + test; after #38/#39/#41 and #55).
- F-120: unused MathJax/arithmatex in `mkdocs.yml`; ride along with the S-6 docs-config fix (neutral title).
- F-121 (A2A/MCP stub pages) and F-123 (`Base#name` ignores `info[:name]`; widen #69): **needs Dewayne's decision**.
- F-115 (dead `simple_json_schema_builder` requires) and F-118 (stale FIXME, `.envrc` header): ride along with the next lib PR touching those files. F-113 (unused public accessors, `Timestamp#utc`): **needs Dewayne's decision**.
- S-20: security finding, details held locally; small neutral-titled lib PR once the queue clears. S-19, S-21: **needs Dewayne's decision**.
- F-53 (+ F-51/F-40 riding along): registry client hides HTTP error statuses behind a JSON parse error; dead `@id`, `payload` alias, unused `response` (the only `ruby -w` warning in lib), wrong header comment. Same file as #56: stack on it or wait. Keep `register` returning uuid/nil (#42 relies on it).
- F-61: CHANGELOG `[Unreleased]` has three near-duplicate #6 lines. Blocked: they are context lines in every one of #38–#53's CHANGELOG hunks; do it after they land.
- F-60: four unused test helpers in `test/support/test_infrastructure.rb` (tests-only removal; same file as #58).
- F-46/F-47/F-50 (with F-39): tests-only cleanups; several touch test files in #38–#54, recheck after they land.
- F-1: `AgentDiscovery#add_agent` zero callers; blocked by #40 (same file) and the public `Base#agents` question.
- F-13: `spec.require_paths` → `["lib"]` (gemspec; after #46). Also clears S-10.
- gemspec: de-dupe homepage/source URLs (F-8; after #46).
- F-78: example registry needs `rackup` (Sinatra 4); touches `Gemfile.lock` (after #53) and ties to F-12. **needs Dewayne's decision** (where the dep lives).
- F-56/F-70 (supported bunny/json majors), F-68 (unused dev deps), F-73, F-76, F-79 (docs toolchain), F-91 (`TcpMessageClient` fate): **needs Dewayne's decision**.
- S-13, S-14, S-15, S-16: security findings, details held locally; small neutral-titled fixes once the in-flight PRs land.

## Open bomberbot PRs

- [#55](https://github.com/MadBomber/agent99/pull/55) `madbot/shutdown-fini-once` → develop. F-55 + F-104. lib: `handle_shutdown` drops `fini` and exits via `Thread.main.raise(SystemExit.new(0))`; new `test/agent99/test_control_actions.rb` (2 child-process tests). Fixup 9368c76 (2026-11-13 06:20) fails before and passes after, review clean. Not low risk (lib).
- [#56](https://github.com/MadBomber/agent99/pull/56) `madbot/registry-url-path` → develop. F-65 + F-71. lib 3 lines + 2 tests. Review clean in 2 rounds. Not low risk (lib).
- [#57](https://github.com/MadBomber/agent99/pull/57) `madbot/drop-end-notes` → develop. F-59 (part). Deletions only in two lib files + CHANGELOG. Review clean round 1. Not low risk (lib).
- CHANGELOG lines sit at the top, after the #4 line and after the debug_me bullet; the three merge together and with Dewayne's PRs without conflict (checked; re-checked 2026-11-13 06:18 after #55's line was reworded).

## Merged bomberbot PRs

- [#27](https://github.com/MadBomber/agent99/pull/27) → develop a651da1, [#28](https://github.com/MadBomber/agent99/pull/28) → 0d749c8, [#29](https://github.com/MadBomber/agent99/pull/29) → 28a6347: squash-merged by Dewayne 2026-11-12 ~21:45Z within 70 s, no review or comment. Branches deleted by the tender 2026-11-13.
- [#9](https://github.com/MadBomber/agent99/pull/9) `bomberbot/fix-timestamp-decode` → develop 5183c92, merged by Dewayne 2026-11-10 (merge commit, no comment). Fixes #4.
- [#10](https://github.com/MadBomber/agent99/pull/10) → develop bfcd914 (merge commit), [#17](https://github.com/MadBomber/agent99/pull/17) → 703dd17, [#11](https://github.com/MadBomber/agent99/pull/11) → 1d90fed, [#12](https://github.com/MadBomber/agent99/pull/12) → 137555d, [#13](https://github.com/MadBomber/agent99/pull/13) → c2b4e49 (squashes), all merged by Dewayne 2026-11-10 after he had `@copilot` resolve the CHANGELOG conflicts. Branches deleted 2026-11-12.
