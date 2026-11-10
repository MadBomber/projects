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

_Updated 2026-11-10 (third visit)._ Dewayne merged #9 (#4) into develop (5183c92). Tender rebased #10/#11 onto it (both
mergeable). New PRs [#12](https://github.com/MadBomber/agent99/pull/12) (#8) and [#13](https://github.com/MadBomber/agent99/pull/13) (#6); filed
[#14](https://github.com/MadBomber/agent99/issues/14) (`get` NameError without `REQUEST_SCHEMA`), [#15](https://github.com/MadBomber/agent99/issues/15) (`VERSION` not loaded, F-21),
[#16](https://github.com/MadBomber/agent99/issues/16) (`examples/control.rb` TypeError). No test CI; "green" = local suite on Ruby 4.0.9 (put
`~/.rbenv/versions/4.0.9/bin` first on PATH; default 4.1-preview1 can't build rbtree, #7).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- [#3](https://github.com/MadBomber/agent99/issues/3) drop the unused top-level `debug_me` `require`/`include`.
  **PR open** [#10](https://github.com/MadBomber/agent99/pull/10).
- [#4](https://github.com/MadBomber/agent99/issues/4) fix `Timestamp.ts2utc` decode + round-trip test. **PR open**
  [#9](https://github.com/MadBomber/agent99/pull/9).
- [#5](https://github.com/MadBomber/agent99/issues/5) allowlist `spec.files` (+ `documentation_uri`, F-8). **PR open**
  [#11](https://github.com/MadBomber/agent99/pull/11).
- [#6](https://github.com/MadBomber/agent99/issues/6) assign `@logger` before connecting in `NatsMessageClient` (F-18). **PR open** [#13](https://github.com/MadBomber/agent99/pull/13).
- [#7](https://github.com/MadBomber/agent99/issues/7) re-lock `Gemfile.lock` for Ruby 4.0/4.1 (F-10, F-11).
- [#8](https://github.com/MadBomber/agent99/issues/8) delete the class-body `rescue` in `base.rb` (F-16). **PR open** [#12](https://github.com/MadBomber/agent99/pull/12).
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
- [#15](https://github.com/MadBomber/agent99/issues/15) F-21: `require "agent99"` never loads `version.rb` (verified 2026-11-10).
- [#14](https://github.com/MadBomber/agent99/issues/14) `get(:field)` NameError when no `REQUEST_SCHEMA` (F-24).
- [#16](https://github.com/MadBomber/agent99/issues/16) `examples/control.rb:84` TypeError on String control acks (F-25).
- gemspec: de-dupe homepage/source URLs (`gem build` warns).
- CHANGELOG `[Unreleased]` still lacks the two post-0.0.5 commits (each PR adds its own line).

## Open bomberbot PRs

- [#10](https://github.com/MadBomber/agent99/pull/10) `bomberbot/drop-debug-me-require` → develop. Fixes #3. [BREAKING FIX] (no more global `DebugMe` include). Review cap hit, trivial items fixed.
- [#11](https://github.com/MadBomber/agent99/pull/11) `bomberbot/gem-files-allowlist` → develop. Fixes #5; gem 1.5 MB → 80 KB. Review clean. Not auto-merged (touches the gemspec).
- [#12](https://github.com/MadBomber/agent99/pull/12) `bomberbot/drop-class-rescue` → develop. Fixes #8. Review cap hit (contradicting rounds). Touches lib, not low risk.
- [#13](https://github.com/MadBomber/agent99/pull/13) `bomberbot/nats-logger-first` → develop. Fixes #6; widened rescue to StandardError (unreachable server raises Errno). Review cap hit, ended clean.
- Each adds one line under CHANGELOG `[Unreleased]`; every one after the first to merge needs a trivial rebase (keep all lines).

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/agent99/pull/9) `bomberbot/fix-timestamp-decode` → develop 5183c92, merged by Dewayne 2026-11-10 (merge commit, no comment). Fixes #4.
