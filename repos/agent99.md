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

_Updated 2026-11-09 (second visit)._ Bomberbot created `develop` from `main` at 69f0a04
and opened three PRs against it, one per first-visit issue: [#9](https://github.com/MadBomber/agent99/pull/9) (#4),
[#10](https://github.com/MadBomber/agent99/pull/10) (#3), [#11](https://github.com/MadBomber/agent99/pull/11) (#5). First dependencies audit ran; filed
[#6](https://github.com/MadBomber/agent99/issues/6) (NATS connect failure masked by NoMethodError),
[#7](https://github.com/MadBomber/agent99/issues/7) (lockfile won't install on Ruby 4.0 / build on 4.1) and
[#8](https://github.com/MadBomber/agent99/issues/8) (dead class-level rescue in `Base`). Suite green on Ruby 4.0.9
only after `bundle install` re-resolves the lock (#7); no test CI (only `docs.yml`).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- [#3](https://github.com/MadBomber/agent99/issues/3) drop the unused top-level `debug_me` `require`/`include`.
  **PR open** [#10](https://github.com/MadBomber/agent99/pull/10).
- [#4](https://github.com/MadBomber/agent99/issues/4) fix `Timestamp.ts2utc` decode + round-trip test. **PR open**
  [#9](https://github.com/MadBomber/agent99/pull/9).
- [#5](https://github.com/MadBomber/agent99/issues/5) allowlist `spec.files` (+ `documentation_uri`, F-8). **PR open**
  [#11](https://github.com/MadBomber/agent99/pull/11).
- [#6](https://github.com/MadBomber/agent99/issues/6) assign `@logger` before connecting in `NatsMessageClient` (F-18).
- [#7](https://github.com/MadBomber/agent99/issues/7) re-lock `Gemfile.lock` for Ruby 4.0/4.1 (F-10, F-11).
- [#8](https://github.com/MadBomber/agent99/issues/8) delete the class-body `rescue` in `base.rb` (F-16).
- F-2: `test/agent99/test_base.rb:44-66` asserts methods only the test double has
  (public `receive_*`/`send_response` are private on `Base`; `discover_agents` etc. don't
  exist). Top candidate next visit: assert against `Agent99::Base` itself.
- F-1: `AgentDiscovery#add_agent` (`agent_discovery.rb:36`) has zero callers; private.
- F-6: README Quick Start / Usage teaches overriding private `process_request` and
  `payload.dig(:name)`; examples use `receive_request` + `get(:field)`. Three README
  links point at missing examples.
- F-7: `examples/maxwell_agent86.rb:97-99` `receive_response(response)` wrong arity,
  `loger` typo, non-interpolated string.
- F-13: `spec.require_paths = ["lib", "examples"]` → `["lib"]` (after #11 merges).
- F-14: declare `logger` as a runtime dependency (no longer a default gem on Ruby 4).
- F-21: `require "agent99"` never loads `version.rb`, so `Agent99::VERSION` is undefined
  (builder note, unverified).
- gemspec: de-dupe homepage/source URLs (`gem build` warns).
- CHANGELOG `[Unreleased]` still lacks the two post-0.0.5 commits (each PR adds its own line).

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/agent99/pull/9) `bomberbot/fix-timestamp-decode` → develop. Fixes #4. Review cap hit, round-3 items fixed.
- [#10](https://github.com/MadBomber/agent99/pull/10) `bomberbot/drop-debug-me-require` → develop. Fixes #3. [BREAKING FIX] (no more global `DebugMe` include). Review cap hit, trivial items fixed.
- [#11](https://github.com/MadBomber/agent99/pull/11) `bomberbot/gem-files-allowlist` → develop. Fixes #5; gem 1.5 MB → 80 KB. Review clean. Not auto-merged (touches the gemspec).
- All three add one line under CHANGELOG `[Unreleased]`; the second and third to merge need a trivial conflict fix (keep both lines).

## Merged bomberbot PRs

- (none)
