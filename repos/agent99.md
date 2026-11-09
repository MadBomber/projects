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

_Updated 2026-11-09 (first visit)._ **Blocked for PRs: the repo has no `develop` branch**
(only `main`, `gh-pages`, and the already-merged `meshage`/`playing_with_p2p`), so
bomberbot couldn't branch from or target it; creating it is Dewayne's step (see
`dewayne.md`). Suite green on Ruby 4.0.9 (57 runs, 0 failures, 4 skips); no test CI runs
it (only `docs.yml` deploys mkdocs). First security audit ran. Filed
[#3](https://github.com/MadBomber/agent99/issues/3) (`require "agent99"` raises
`LoadError: debug_me` on a clean install — dev-only dependency required at runtime),
[#4](https://github.com/MadBomber/agent99/issues/4) (`Agent99::Timestamp` round-trip is
broken — bit-shift decode vs decimal encode, >2-year drift) and
[#5](https://github.com/MadBomber/agent99/issues/5) (published gem is ~1.5 MB: docs
images, a binary `.db`, and planning notes ship).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo, once `develop` exists.

- [#3](https://github.com/MadBomber/agent99/issues/3) make `debug_me` a runtime dep or
  drop the unused top-level `require`/`include` (C1).
- [#4](https://github.com/MadBomber/agent99/issues/4) fix `Timestamp.ts2utc` to decode
  with decimal arithmetic + add a round-trip test (C2).
- [#5](https://github.com/MadBomber/agent99/issues/5) allowlist `spec.files` (C1 triage).
- F-1: `AgentDiscovery#add_agent` (`agent_discovery.rb:36`) has zero callers — dead code.
- F-2: `test/agent99/test_base.rb:57-65` asserts methods that exist only on the test
  double (`discover_agents`, `get_all_agents`, `send_request`, `create_header`,
  `validate_header`), not on the real `Agent99::Base`. False confidence; fix the asserts.
- README Quick Start / Usage teaches overriding the private `process_request` dispatcher
  and reading `payload.dig(:name)`; every shipped example overrides `receive_request` and
  uses `get(:field)`. README also links `examples/greeter_agent.rb`,
  `examples/multi_agent/`, `examples/dashboard/` which don't exist. Fix docs to match code.
- `examples/maxwell_agent86.rb:97-99` `receive_response(response)` — wrong arity, `loger`
  typo, non-interpolated string. Shipped sample code that is dead on arrival.
- gemspec: add `documentation_uri` (docs site is live); de-dupe homepage/source URLs.
- CHANGELOG `[Unreleased]` empty despite two post-0.0.5 commits.

## Open bomberbot PRs

- (none — repo blocked for PRs until `develop` exists)

## Merged bomberbot PRs

- (none)
