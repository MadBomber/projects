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

_Updated 2026-11-12 (seventh visit)._ Dewayne merged all six earlier bomberbot PRs (#9–#13, #17) into `develop` (now c2b4e49), so the queue cleared and this visit opened three: [#27](https://github.com/MadBomber/agent99/pull/27) (#20), [#28](https://github.com/MadBomber/agent99/pull/28) (#21+#15), [#29](https://github.com/MadBomber/agent99/pull/29) (#23+F-58). The three merge together without CHANGELOG conflicts. Dead-code audit at c2b4e49 (F-58..F-61); filed #30–#32. No test CI; "green" = local suite on Ruby 4.0.9 with a locally re-resolved lock (put `~/.rbenv/versions/4.0.9/bin` first on PATH; the committed lock doesn't install, #7).


## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything
else in this repo.

- #3, #4, #5, #6, #8 and F-2: fixed on `develop` by #10, #9, #11, #13, #12, #17. The issues stay open until develop reaches `main`.
- [#7](https://github.com/MadBomber/agent99/issues/7) re-lock `Gemfile.lock` for Ruby 4.0/4.1 (F-10, F-11). Raised priority 2026-11-10: the re-lock also clears S-8 (held locally); stay within majors.
- [#21](https://github.com/MadBomber/agent99/issues/21) F-23: `require "agent99"` doesn't load `HeaderSchema`; README step 1 NameErrors. **PR open** [#28](https://github.com/MadBomber/agent99/pull/28) (with #15).
- [#23](https://github.com/MadBomber/agent99/issues/23) F-27: `examples/registry.rb:29` `request.body.rewind` 500s on WEBrick/Rack 3 (one-line delete). **PR open** [#29](https://github.com/MadBomber/agent99/pull/29) (with F-58).
- F-1: `AgentDiscovery#add_agent` (`agent_discovery.rb:36`) has zero callers; private.
- [#24](https://github.com/MadBomber/agent99/issues/24) F-6: README Quick Start / Usage teaches overriding private `process_request` and
  `payload.dig(:name)`; examples use `receive_request` + `get(:field)`. A follower's first agent crashes on a nil `@payload`. Filed this visit.
- [#22](https://github.com/MadBomber/agent99/issues/22) F-7: `examples/maxwell_agent86.rb:97-99` `receive_response(response)` wrong arity,
  `loger` typo, non-interpolated string.
- F-13: `spec.require_paths = ["lib", "examples"]` → `["lib"]` (unblocked: #11 merged; rename `test_ships_examples_because_they_are_on_the_load_path`; gemspec → not low risk). Also clears S-10 (held locally).
- [#25](https://github.com/MadBomber/agent99/issues/25) F-14: declare `logger` as a runtime dependency (no longer a default gem on Ruby 4). Filed this visit.
- [#26](https://github.com/MadBomber/agent99/issues/26) F-57: no CI runs `bundle install`/tests on any Ruby (only `docs.yml`); lock drift goes unnoticed. Filed this visit. Pair with #7.
- F-56: a plain re-lock jumps `bunny` 2.24→3.6 (major, unguarded by the gemspec, untested vs a real broker). Pin the gemspec when cutting the #7 re-lock. **needs Dewayne's decision** (supported bunny range).
- [#15](https://github.com/MadBomber/agent99/issues/15) F-21: `require "agent99"` never loads `version.rb`. **PR open** [#28](https://github.com/MadBomber/agent99/pull/28).
- [#14](https://github.com/MadBomber/agent99/issues/14) `get(:field)` NameError when no `REQUEST_SCHEMA` (F-37).
- [#16](https://github.com/MadBomber/agent99/issues/16) `examples/control.rb:84` TypeError on String control acks (F-38).
- [#18](https://github.com/MadBomber/agent99/issues/18) header helpers stale inside `receive_response`/`receive_control` (F-42). Unblocked (#12 merged); changes header behaviour, so less obvious.
- [#19](https://github.com/MadBomber/agent99/issues/19) failed registration leaves a running agent with a nil id (F-54).
- [#20](https://github.com/MadBomber/agent99/issues/20) `AmqpMessageClient#setup` ignores its `logger:` (F-43). **PR open** [#27](https://github.com/MadBomber/agent99/pull/27).
- F-55: remote shutdown runs `fini` twice and always logs a false warning (`control_actions.rb:41`).
- F-46/F-47/F-50 (with F-39): tests-only cleanups: assert the flags lifecycle tests set, restore patched AMQP methods in `ensure`, drop unused test assignments. Low-risk candidates.
- F-51 (with F-40): `registry_client.rb` dead `@id`, `payload` alias, wrong header comment.
- F-53: registry client hides HTTP error statuses behind a JSON parse error.
- gemspec: de-dupe homepage/source URLs (`gem build` warns).
- F-61: CHANGELOG `[Unreleased]` has three near-duplicate #6 lines (12-14 at c2b4e49; keep the last). Docs-only, low-risk candidate; open it after #27-#29 merge.
- F-59 (part): delete the pasted chat transcript after `__END__` in `lib/agent99/tcp_message_client.rb:128-183` (and the stale line after `__END__` in `header_schema.rb`). Comments only; describe as cleanup.
- [#30](https://github.com/MadBomber/agent99/issues/30) F-62: `bin/console` requires nonexistent `ai_agent` (pairs with F-32 `sig/ai_agent.rbs`).
- [#31](https://github.com/MadBomber/agent99/issues/31) F-63: six README Key Features links point to missing anchors.
- [#32](https://github.com/MadBomber/agent99/issues/32) F-64: `rake test_coverage`/`test_verbose` do nothing (no simplecov, env vars unread).
- F-60: four unused test helpers in `test/support/test_infrastructure.rb` (tests-only removal).

## Open bomberbot PRs

- [#27](https://github.com/MadBomber/agent99/pull/27) `madbot/amqp-setup-logger` → develop. Fixes #20. lib, one line + test. Review clean round 1.
- [#28](https://github.com/MadBomber/agent99/pull/28) `madbot/load-all-constants` → develop. Fixes #21, #15. lib, two `require_relative` + load test (runs a child without the gemspec's VERSION). Review clean round 1.
- [#29](https://github.com/MadBomber/agent99/pull/29) `madbot/registry-no-rewind` → develop. Fixes #23 + F-58 (examples only, deletes 4 lines). Review clean in 2 rounds. Not self-merged: it deletes code lines.
- Their CHANGELOG lines sit at the top, middle and end of `[Unreleased]` → Fixed; all three merge together without conflict (checked).

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/agent99/pull/9) `bomberbot/fix-timestamp-decode` → develop 5183c92, merged by Dewayne 2026-11-10 (merge commit, no comment). Fixes #4.
- [#10](https://github.com/MadBomber/agent99/pull/10) → develop bfcd914 (merge commit), [#17](https://github.com/MadBomber/agent99/pull/17) → 703dd17, [#11](https://github.com/MadBomber/agent99/pull/11) → 1d90fed, [#12](https://github.com/MadBomber/agent99/pull/12) → 137555d, [#13](https://github.com/MadBomber/agent99/pull/13) → c2b4e49 (squashes), all merged by Dewayne 2026-11-10 after he had `@copilot` resolve the CHANGELOG conflicts. Branches deleted 2026-11-12.
