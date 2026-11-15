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

_Updated 2026-11-15 01:50 (seventeenth visit)._ Dewayne merged #38–#57 and #77–#95 on 2026-11-13 (develop 28a6347 → 3a338b8, CI green); this visit merged #96 as low risk (develop 7d79e63), opened #97 and #101, and filed #98–#100.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- **In flight in Dewayne's own PR:** #69 → [#88](https://github.com/MadBomber/agent99/pull/88), which is CONFLICTING with develop (needs his rebase). Off limits while open: `lib/agent99/agent_lifecycle.rb`, `test/agent99/test_agent_lifecycle.rb`, `docs/operations/breaking-changes.md`, the top of CHANGELOG `[Unreleased]` → Fixed.
- #3–#8, #14–#37, #58–#76: fixed on `develop` and closed by Dewayne (develop is 57+ commits ahead of `main`).
- F-61: duplicate NATS CHANGELOG lines. **Merged (low risk)** [#96](https://github.com/MadBomber/agent99/pull/96) → develop 7d79e63.
- S-12/S-17: development lock update. **PR open** [#97](https://github.com/MadBomber/agent99/pull/97) (neutral title; Gemfile.lock, not low risk).
- F-53 + F-51 + F-40 + F-128: registry client hides HTTP status behind a JSON parse error; dead `@id`/`@base_url`, alias, unused `response`. **PR open** [#101](https://github.com/MadBomber/agent99/pull/101) (lib; review cap hit).
- [#98](https://github.com/MadBomber/agent99/issues/98) F-132: `examples/control.rb` "stop all" shuts down the controller; `fini` runs twice (examples, ~3 lines).
- [#99](https://github.com/MadBomber/agent99/issues/99) F-130 + F-131: API reference custom registry client interface is wrong; messaging page claims AMQP persistence (docs-only, ~12 lines; low-risk candidate).
- [#100](https://github.com/MadBomber/agent99/issues/100) F-134: agent stays registered when `init` exits or raises (lib 2 lines + test; after #88).
- F-60 + F-129: unused test helpers and orphaned mock `create_queue`/`@config` in `test/support/test_infrastructure.rb` (tests-only removal; drop `:server` from F-60, it is used now).
- F-115 + F-118 + F-103: redundant `simple_json_schema_builder` requires (`base.rb:5`, `amqp_message_client.rb:5`; keep `agent99.rb:4`), stale FIXME (`amqp_message_client.rb:90`), `.envrc` header, unused `lib/agent99/.irbrc` (small lib removal).
- F-1: `AgentDiscovery#add_agent` has zero callers (unblocked; but it is the only writer of `@agents` behind public `Base#agents`, which is a decision).
- F-13 + F-8: gemspec `require_paths` → `["lib"]`, de-dupe homepage/source URIs (gemspec, never low risk; also clears S-10).
- F-133: lifecycle/control docs don't say shutdown and failed registration exit the process (docs-only, ~6 lines).
- F-135: `brew install rabbitmq-server` (the formula is `rabbitmq`) in 6 places; DEMO.md says Ruby 3.0+ (docs/examples ~7 lines).
- F-136 (no CHANGELOG line for #54's CI), F-137 (`Base#run` dispatcher untested), F-138 (method-redefinition warnings in client tests), F-46/F-47/F-50: small tests/docs cleanups; F-46 waits on #88.
- F-6/F-34: README dead links (`README.md:181,231-233,266`), now unblocked (docs-only).
- S-6 + F-120: docs-config fix in `mkdocs.yml` (neutral title; never low risk).
- S-13, S-14, S-15, S-20: security findings, details held locally; small neutral-titled fixes.
- **Needs Dewayne's decision:** F-26 (docs describe an API that does not exist), F-41, F-45, F-48, F-52, F-56/F-70 (bunny 3 / json 3 majors), F-68, F-73, F-76, F-78 (`rackup` for the example registry), F-79, F-91, F-94, F-102, F-113, F-121, F-123, F-126, F-127, `Base#agents`, gemspec floors for sinatra/rack; S-19, S-21.

## Open bomberbot PRs

- [#97](https://github.com/MadBomber/agent99/pull/97) `bomberbot/relock-web-deps` → develop. Conservative `--minor` re-lock of rack, rack-session, rack-protection, sinatra, json (stays 2.x), uri; lock 7/7 lines + 1 CHANGELOG line (last bullet of Fixed). Suite 113/0 on 4.0.9 and 4.1.0-preview1, example registry routes checked, CI green, review clean in 2 rounds. Not low risk (Gemfile.lock).
- [#101](https://github.com/MadBomber/agent99/pull/101) `bomberbot/registry-http-errors` → develop. F-53/F-51/F-40/F-128: logs `Registry returned <code> <reason>[: error]`, return values unchanged; the test file fails on develop (3F 1E) and passes on the branch, suite 117/0, `ruby -wc` lib clean, CI green. Review hit its 3-round cap (last fixes not re-reviewed). Not low risk (lib).
- CHANGELOG: #101 inserts after the #55 "remote shutdown" line, #97 at the end of Fixed, #88 at the top; #97 + #101 merge together cleanly and neither conflicts with #88 in CHANGELOG (checked 2026-11-15).

## Merged bomberbot PRs

- [#96](https://github.com/MadBomber/agent99/pull/96) → develop 7d79e63: **merged (low risk)** by bomberbot 2026-11-15 (rebase). F-61.
- [#55](https://github.com/MadBomber/agent99/pull/55) → develop 5762f8e, [#56](https://github.com/MadBomber/agent99/pull/56) → 9f0042b, [#57](https://github.com/MadBomber/agent99/pull/57) → 1db8ef9: squash-merged by Dewayne 2026-11-13 ~21:23Z, no comment. Branches gone.
- [#27](https://github.com/MadBomber/agent99/pull/27) → develop a651da1, [#28](https://github.com/MadBomber/agent99/pull/28) → 0d749c8, [#29](https://github.com/MadBomber/agent99/pull/29) → 28a6347: squash-merged by Dewayne 2026-11-12 ~21:45Z within 70 s, no review or comment.
- [#9](https://github.com/MadBomber/agent99/pull/9) → develop 5183c92 (merge commit, 2026-11-10). Fixes #4.
- [#10](https://github.com/MadBomber/agent99/pull/10) → develop bfcd914 (merge commit), [#17](https://github.com/MadBomber/agent99/pull/17) → 703dd17, [#11](https://github.com/MadBomber/agent99/pull/11) → 1d90fed, [#12](https://github.com/MadBomber/agent99/pull/12) → 137555d, [#13](https://github.com/MadBomber/agent99/pull/13) → c2b4e49 (squashes), merged by Dewayne 2026-11-10.
