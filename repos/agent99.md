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

_Updated 2026-11-10 (sixth visit)._ Tend-and-record again: **skipped new PRs — review queue full** (#10–#13 and #17 still open, unapproved, all MERGEABLE, 0 behind develop, no review threads; `develop` still 5183c92, unmoved since #9). Second **dependencies** audit (most overdue; committed lock still un-installable on Ruby 4.0.9/4.1-preview1 and carries ~34 bundle-audit advisories — S-12, held locally; re-resolved lock installs clean + green 64/0/0/4 on both Rubies; `rbtree` re-resolves to 0.4.7). New F-56 (re-lock jumps bunny 2.24→3.6 major, needs-decision), F-57/#26 (no Ruby CI); re-verified F-14/#25 (logger undeclared), #7. Responder linked PRs on #3/#5/#6/#8; no new design comments. Filed [#24](https://github.com/MadBomber/agent99/issues/24) (README teaches crashing `process_request`, F-6), [#25](https://github.com/MadBomber/agent99/issues/25) (declare `logger` runtime dep, F-14), [#26](https://github.com/MadBomber/agent99/issues/26) (no Ruby test CI, F-57). No test CI; "green" = local suite on Ruby 4.0.9 (put
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
- [#7](https://github.com/MadBomber/agent99/issues/7) re-lock `Gemfile.lock` for Ruby 4.0/4.1 (F-10, F-11). Raised priority 2026-11-10: the re-lock also clears S-8 (held locally); stay within majors.
- [#8](https://github.com/MadBomber/agent99/issues/8) delete the class-body `rescue` in `base.rb` (F-16). **PR open** [#12](https://github.com/MadBomber/agent99/pull/12).
- F-2: `test/agent99/test_base.rb:44-66` asserts methods only the test double has
  (public `receive_*`/`send_response` are private on `Base`; `discover_agents` etc. don't
  exist). **PR open** [#17](https://github.com/MadBomber/agent99/pull/17) (needs-dewayne: header-helper visibility).
- [#21](https://github.com/MadBomber/agent99/issues/21) F-23: `require "agent99"` doesn't load `HeaderSchema`; README step 1 NameErrors. Pair with #15 after #10. Top PR candidate once the queue clears.
- [#23](https://github.com/MadBomber/agent99/issues/23) F-27: `examples/registry.rb:29` `request.body.rewind` 500s on WEBrick/Rack 3 (one-line delete).
- F-1: `AgentDiscovery#add_agent` (`agent_discovery.rb:36`) has zero callers; private.
- [#24](https://github.com/MadBomber/agent99/issues/24) F-6: README Quick Start / Usage teaches overriding private `process_request` and
  `payload.dig(:name)`; examples use `receive_request` + `get(:field)`. A follower's first agent crashes on a nil `@payload`. Filed this visit.
- [#22](https://github.com/MadBomber/agent99/issues/22) F-7: `examples/maxwell_agent86.rb:97-99` `receive_response(response)` wrong arity,
  `loger` typo, non-interpolated string.
- F-13: `spec.require_paths = ["lib", "examples"]` → `["lib"]` (after #11 merges). Also clears S-10 (held locally).
- [#25](https://github.com/MadBomber/agent99/issues/25) F-14: declare `logger` as a runtime dependency (no longer a default gem on Ruby 4). Filed this visit.
- [#26](https://github.com/MadBomber/agent99/issues/26) F-57: no CI runs `bundle install`/tests on any Ruby (only `docs.yml`); lock drift goes unnoticed. Filed this visit. Pair with #7.
- F-56: a plain re-lock jumps `bunny` 2.24→3.6 (major, unguarded by the gemspec, untested vs a real broker). Pin the gemspec when cutting the #7 re-lock. **needs Dewayne's decision** (supported bunny range).
- [#15](https://github.com/MadBomber/agent99/issues/15) F-21: `require "agent99"` never loads `version.rb` (verified 2026-11-10).
- [#14](https://github.com/MadBomber/agent99/issues/14) `get(:field)` NameError when no `REQUEST_SCHEMA` (F-37).
- [#16](https://github.com/MadBomber/agent99/issues/16) `examples/control.rb:84` TypeError on String control acks (F-38).
- [#18](https://github.com/MadBomber/agent99/issues/18) header helpers stale inside `receive_response`/`receive_control` (F-42). Build after #12.
- [#19](https://github.com/MadBomber/agent99/issues/19) failed registration leaves a running agent with a nil id (F-54).
- [#20](https://github.com/MadBomber/agent99/issues/20) `AmqpMessageClient#setup` ignores its `logger:` (F-43). One line plus a test.
- F-55: remote shutdown runs `fini` twice and always logs a false warning (`control_actions.rb:41`).
- F-46/F-47/F-50 (with F-39): tests-only cleanups: assert the flags lifecycle tests set, restore patched AMQP methods in `ensure`, drop unused test assignments. Low-risk candidates.
- F-51 (with F-40): `registry_client.rb` dead `@id`, `payload` alias, wrong header comment.
- F-53: registry client hides HTTP error statuses behind a JSON parse error.
- gemspec: de-dupe homepage/source URLs (`gem build` warns).
- CHANGELOG `[Unreleased]` still lacks the two post-0.0.5 commits (each PR adds its own line).

## Open bomberbot PRs

- [#10](https://github.com/MadBomber/agent99/pull/10) `bomberbot/drop-debug-me-require` → develop. Fixes #3. [BREAKING FIX] (no more global `DebugMe` include). Review cap hit, trivial items fixed.
- [#11](https://github.com/MadBomber/agent99/pull/11) `bomberbot/gem-files-allowlist` → develop. Fixes #5; gem 1.5 MB → 80 KB. Review clean. Not auto-merged (touches the gemspec).
- [#12](https://github.com/MadBomber/agent99/pull/12) `bomberbot/drop-class-rescue` → develop. Fixes #8. Review cap hit (contradicting rounds). Touches lib, not low risk.
- [#13](https://github.com/MadBomber/agent99/pull/13) `bomberbot/nats-logger-first` → develop. Fixes #6; widened rescue to StandardError (unreachable server raises Errno). Review cap hit, ended clean.
- [#17](https://github.com/MadBomber/agent99/pull/17) `bomberbot/test-real-base` → develop. F-2, tests only. `needs-dewayne` (pins header helpers public). Review cap hit, round 3 no defects.
- Each adds one line under CHANGELOG `[Unreleased]`; every one after the first to merge needs a trivial rebase (keep all lines).

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/agent99/pull/9) `bomberbot/fix-timestamp-decode` → develop 5183c92, merged by Dewayne 2026-11-10 (merge commit, no comment). Fixes #4.
