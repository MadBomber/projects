# Findings: agent99

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-13 | Fourth run, develop 28a6347. bundle-audit 0.9.3, DB 4d71b12 (= upstream HEAD): committed lock 31 advisories; #53 lock 28 (S-17 stands); fresh re-resolve clean. Full lib re-read + examples sinks. New S-19..S-21 (held locally). S-1..S-15, S-17 re-confirmed; S-16 fix in PR #56. Earlier: 2026-11-12 Third run, develop c2b4e49. bundle-audit 0.9.3, DB 4d71b12: committed lock 31 advisories; re-resolved lock clean. S-1..S-12 re-confirmed open. New F-65, S-13..S-15 (held locally). Earlier: 2026-11-10 develop 5183c92 (S-9..S-11), 2026-11-09 main 69f0a04. |
| dead code | 2026-11-13 | Full pass, develop 28a6347. debride 1.15.2 (scratch GEM_HOME) on lib and on lib+examples+test+bin/console, Ruby 4.1.0-preview1; hand-verified; `ruby -wc` on lib and examples; dependent `agent99_examples` (42a1701, pins 0.0.4) uses none of the candidates. New F-113 (unused public accessors + `Timestamp#utc`, needs decision), F-114 (AMQP unit tests test only the mock), F-115 (two dead `simple_json_schema_builder` requires). Re-confirmed F-1, F-40, F-41, F-42, F-51, F-52, F-59/F-91, F-60, F-73, F-103. Live: `handle_*` via `send`, `HeaderManagement` readers, `Base` client setters, `Agent99.version`. Earlier: delta 2026-11-13 c2b4e49..28a6347; full 2026-11-12 c2b4e49. |
| dependencies | 2026-11-13 | Third run, develop 28a6347, Ruby 4.0.9 + 4.1.0-preview1. Committed lock still fails to install on 4.0.9 (#7). Fresh re-resolve green 74/0/0/4 and bundle-audit clean; committed lock 31 advisories. #53+#46+#54 merged in scratch: frozen install green on 4.0.9/4.1, still 28 advisories (S-17). New F-93 (issue #63), F-94, S-17. Re-confirmed F-14 (#46 sufficient), F-56 (deferred), F-57 (#54; install unfrozen, no bundle-audit step), #7 (#53 sufficient for install). Floor 3.3 OK until 2027-03-31 EOL. Earlier: 2026-11-10 5183c92, 2026-11-09 69f0a04. |
| adoption | 2026-11-13 | Second run, develop 28a6347. `mkdocs build --strict` passes (Material EOL 2027-05-05 notice, F-79); `gem build` 81 KB / 33 files, dup-URI warning (F-8). README step 1 works (F-21/F-23 fixed by #28); registry 500 fixed by #29 but it still needs `rackup` (F-78). New F-77..F-79. Still present: F-6 (missing example links; method part in #45), F-8, F-20, F-24..F-26, F-28..F-35, F-61. gem-skill not run (paid API). First run 2026-11-10, develop 5183c92. |
| quality | 2026-11-13 | Third run, develop 28a6347, Ruby 4.0.9 (scratch GEM_HOME). flog 634.4 (avg 7.6), flay 256, reek 6.6.0 83, rubocop 1.92.2 221 lib / 989 test (defaults + pending cops, no repo config; not comparable with the earlier 249/878, which used a different rubocop). `ruby -wc`: one warning (F-40). New F-104 (fixed in #55). The auditor's S-18 was S-7. Re-confirmed F-40..F-42, F-44, F-51, F-53, F-73, F-76, F-101. No live broker in the sandbox; reproduced in-process with the real bunny 2.24.0 / nats-pure 2.7.0 thread pools. Earlier: 2026-11-12 c2b4e49, 2026-11-10 5183c92. |

Dead-code scan last covered commit: 28a6347 (develop, full pass 2026-11-13)

## Findings

### 2026-11-09 (first visit; security audit + dead-code note, main 69f0a04)

- S-1: security finding, details held locally. Status: open (HIGH; control-plane auth).
- S-2: security finding, details held locally. Status: open (HIGH; disabled validation).
- S-3: security finding, details held locally. Status: open (MEDIUM; TCP transport).
- S-4: security finding, details held locally. Status: open (MEDIUM; reference registry).
- S-5: security finding, details held locally. Status: open (LOW; transport defaults).
- F-1: `AgentDiscovery#add_agent` (`lib/agent99/agent_discovery.rb:36`) has zero callers
  across `lib/`, `examples/`, and `test/`. Private, undocumented — a clean removal.
  Status: open, verified. (Candidate once `develop` exists.)
- F-2: `test/agent99/test_base.rb:57-65` asserts `Agent99::Base` instances respond to
  `:discover_agents`, `:get_all_agents`, `:send_request`, `:create_header`,
  `:validate_header` — none exist in `lib/`; they're defined only on the test double
  `TestInfrastructure::TestAgent` (`test/support/test_infrastructure.rb`). The real API
  is `discover_agent` (singular) and `fetch_all_agents`. Green tests give false
  confidence. Fix: assert against real method names. Status: open, verified.
- F-3: `require "agent99"` raises `LoadError: debug_me` on a clean install. `lib/agent99.rb:3-4`
  does `require 'debug_me'` + `include DebugMe` at load time, but `debug_me` is a
  development dependency only (`agent99.gemspec:50`) and used nowhere else in `lib/`.
  Reproduced (`ruby -e 'require "./lib/agent99"'` → LoadError without the dev bundle).
  Status: issue [#3](https://github.com/MadBomber/agent99/issues/3); PR open [#10](https://github.com/MadBomber/agent99/pull/10).
- F-4: `Agent99::Timestamp` round-trip is broken. Encode `timestamp.rb:27`
  (`now.to_i * 1_000_000 + now.usec`, decimal) vs decode `timestamp.rb:32`
  (`Time.at(microseconds >> 20, microseconds & 0xFFFFF)`, binary). Reproduced: a 2026
  time round-trips to 2024 (>2-year drift). No test covers `ts2utc`. Fix:
  `Time.at(microseconds / 1_000_000, microseconds % 1_000_000).utc`. Status: issue
  [#4](https://github.com/MadBomber/agent99/issues/4); PR open [#9](https://github.com/MadBomber/agent99/pull/9).
- F-5: published gem is ~1.5 MB. `spec.files` (`agent99.gemspec:35-41`) uses `git ls-files`
  minus a tiny denylist, so docs images (~1.3 MB logo PNG), a binary `docs/AI/htm_demo.db`,
  planning notes (`A2A_SPEC-dev.md`, `p2p_plan.md`, `p2p_roadmap.md`, `registry_plan.md`),
  `COMMITS.md`, `DOCS.md`, `Rakefile`, `mkdocs.yml`, and `lib/agent99/.irbrc` all ship.
  Reproduced via `gem build` (1,568,256 bytes). Fix: allowlist `spec.files`. Status:
  issue [#5](https://github.com/MadBomber/agent99/issues/5); PR open [#11](https://github.com/MadBomber/agent99/pull/11).
- F-6: README Quick Start/Usage teaches overriding the private `process_request`
  dispatcher and reading `payload.dig(:name)` (`README.md:147-150`); the actual contract
  (every shipped example) overrides `receive_request` (no args) and uses `get(:field)`.
  README also links `examples/greeter_agent.rb`, `examples/multi_agent/`,
  `examples/dashboard/`, none of which exist. Fix docs to match code. Status: open,
  verified. (Candidate once `develop` exists.)
- F-7: `examples/maxwell_agent86.rb:97-99` `receive_response(response)` — wrong arity
  (framework calls it with no args, `message_processing.rb:47`), `loger` typo
  (NoMethodError), and a non-interpolated `"...response.inspect"` literal. Shipped sample
  code, dead on arrival on the error path. Status: issue [#22](https://github.com/MadBomber/agent99/issues/22) (filed 2026-11-10).
- F-8: gemspec adoption nits: no `documentation_uri` though the docs site is live
  (`agent99.gemspec:30-32`); `homepage_uri` and `source_code_uri` are the same URL
  (`gem build` warns); gemspec `description` claims NLP/"conversing with AI models" while
  the README says the release has no AI components. documentation_uri is a pure add; the
  description rewrite is content → inbox. Status: `documentation_uri` PR open [#11](https://github.com/MadBomber/agent99/pull/11); URL de-dupe + description still open.
- F-9: CHANGELOG `[Unreleased]` (`CHANGELOG.md:3-4`) is empty despite two post-0.0.5
  commits (`69f0a04` test, `0db4210` docs). Minor discipline gap. Status: open, verified.

### 2026-11-09 (second visit; dependencies audit + improver, develop 69f0a04)

- F-2 update: the scope is wider. `test_agent_responds_to_message_methods` (`test_base.rb:44-52`)
  also passes only because of the test double: `receive_request`, `receive_response`,
  `receive_control`, `send_response` exist on `Agent99::Base` but are private
  (`message_processing.rb:13`). Status: open, verified (top candidate for next visit).
- F-10: committed `Gemfile.lock` doesn't install as-is on Ruby 4.0.9 (tocer 19.3.0 /
  refinements 13.5.0 / core 2.3.0 need Ruby ~> 3.4; `BUNDLED WITH 2.5.23` spews
  Gem::Platform warnings). Re-resolved, suite is 57/0/0, 4 skips. Status: issue
  [#7](https://github.com/MadBomber/agent99/issues/7).
- F-11: bundle doesn't build on Ruby 4.1.0-preview1: bunny → sorted_set 1.0.3 → rbtree
  0.4.6 (`Data_Wrap_Struct` undeclared). rbtree 0.4.7 / sorted_set 1.1.0 build. Status:
  issue [#7](https://github.com/MadBomber/agent99/issues/7) (with F-10).
- F-12: `sinatra` is a runtime dependency (`agent99.gemspec:47`) the library never
  loads; only `examples/registry.rb` uses it. Status: needs Dewayne's decision (inbox).
- F-13: `spec.require_paths = ["lib", "examples"]` (`agent99.gemspec:42`) puts
  `registry.rb`, `control.rb`, etc. on every consumer's `$LOAD_PATH`. Nothing relies on
  it (all example requires are `require_relative`). Fix: `["lib"]`. Status: open,
  verified (gemspec; sequence after the #5 PR).
- F-14: `logger` is required in four lib files but not declared; it's no longer a default
  gem on Ruby 4.0 and only resolves through bunny. Fix: `spec.add_dependency "logger"`.
  Status: open, verified.
- F-15: no runtime dependency has a version constraint; bunny 3.6.0 is out vs locked
  2.24.0. Status: unverified (bunny 3 untested), needs Dewayne's decision on bounds.
- F-16: class-body `rescue` in `lib/agent99/base.rb:56-59` can only catch load-time
  errors, would raise NoMethodError (`handle_error` is an instance method), and warns
  under `-w`. Status: issue [#8](https://github.com/MadBomber/agent99/issues/8).
- F-17: no CI runs the tests (`.github/workflows/` has only `docs.yml`); no
  `dependabot.yml`. Status: needs Dewayne's decision on the Ruby matrix (inbox).
- F-18: `NatsMessageClient#initialize` connects before assigning `@logger`, so a connect
  failure raises `NoMethodError ... for nil` instead of the intended message
  (`nats_message_client.rb:17-20`). Reproduced by the main session. Status: issue
  [#6](https://github.com/MadBomber/agent99/issues/6); PR open [#13](https://github.com/MadBomber/agent99/pull/13).
- F-19: message hooks (`receive_request`, `send_response`, ...) are private on `Base`
  while README/docs present them as overridable API and subclasses redefine them
  public. Status: needs Dewayne's decision (document "override as private" vs move above
  `private`).
- F-20: README lists env vars the library never reads (`AGENT99_LOG_LEVEL`, `NATS_*`,
  `RABBITMQ_*`); only `AGENT99_REGISTRY_URL` is read. Taste says change the code, but it's
  three files and a precedence/defaults choice. Status: needs Dewayne's decision (inbox).
- F-21: `require "agent99"` doesn't load `lib/agent99/version.rb`, so `Agent99::VERSION`
  is undefined after a plain require (builder observation on #10). Status: issue [#15](https://github.com/MadBomber/agent99/issues/15) (verified 2026-11-10).
- F-22: `examples/example_agent.rb` uses `require_relative '../../lib/agent99'`, which
  points outside the installed gem. Status: rejected 2026-11-10 (by design: meant to be copied into `examples/agents/`, as `run_demo.rb:295` does).

### 2026-11-10 (third visit; adoption audit + improver, develop 5183c92)

- F-2 update: PR open [#17](https://github.com/MadBomber/agent99/pull/17) (tests only; needs-dewayne on header-helper visibility).
- F-6 update: the README `process_request(payload)` pattern crashes on every request. It skips
  the `@payload =` assignment (`message_processing.rb:37`), so `send_response` → `return_address`
  raises NoMethodError (`header_management.rb:17`); on NATS the caller never gets a reply and
  nothing is logged (F-36). README also puts `require 'agent99'` (`README.md:156`) after the
  classes that need it. Status: open, verified.
- F-8 update: also missing `bug_tracker_uri`, `rubygems_mfa_required`; #11's `documentation_uri`
  points at a site that is currently 404 (F-25).
- F-9 update: rejected. `v0.0.5` is an annotated tag on 69f0a04, so both commits are in 0.0.5.
- F-20 update: also in `docs/getting-started/installation.md:125-129` and
  `docs/framework-components/messaging-system.md:57-61`; `NATS.connect` takes no args
  (`nats_message_client.rb:95`), so the NATS URL isn't configurable at all.
- F-23: `require "agent99"` doesn't load `Agent99::HeaderSchema` (`lib/agent99/header_schema.rb`
  required by nothing); README step 1 (`README.md:129`) and ~20 doc snippets use it →
  NameError. Fix: `require_relative 'agent99/header_schema'` in `lib/agent99.rb` with F-21,
  after #10. Status: issue [#21](https://github.com/MadBomber/agent99/issues/21) (filed 2026-11-10; verified still open on develop 5183c92).
- F-24: README/docs call NATS "Recommended" (`README.md:69`, `quick-start.md:15`) but `Base`
  defaults to AMQP (`agent_lifecycle.rb:12`); no doc shows passing `NatsMessageClient`. Status:
  needs Dewayne's decision (default vs document the selector).
- F-25: docs site https://madbomber.github.io/agent99/ is 404; Pages API 404 (disabled);
  gh-pages last deployed 2025-09-03. Status: needs Dewayne's decision (repo setting; after S-6).
- F-26: `docs/api-reference/agent99-base.md` documents 12 instance methods and `Base.create`
  that don't exist; 12 docs pages override `process_request`; `discover_agents`/`send_request`
  in ~20 places. Status: needs Dewayne's decision (rewrite docs vs add conveniences).
- F-27: example registry won't start from the repo bundle (no `rackup`), and on WEBrick
  `POST /register` 500s: `request.body.rewind` (`examples/registry.rb:29`) on non-rewindable
  Rack 3 input. Works on Puma or with line 29 deleted. Status: issue [#23](https://github.com/MadBomber/agent99/issues/23) (filed 2026-11-10).
- F-28: `examples/agent_watcher.rb` defines pre-0.0.4 `capabilities`, no `info` → exits 1;
  requires `listen` (not bundled); `kaos_spy.rb:12` requires `tty-table` (not bundled).
  Status: open, verified.
- F-29: `run_demo.rb:184`, `examples/DEMO.md:98,115` promise a message-client fallback that
  doesn't exist. Status: needs Dewayne's decision (tied to F-24).
- F-30: `mkdocs.yml:135,138` social links go to `madbomber/agemt99` (typo) labelled "AIA";
  `docs.yml:35` installs unused `mkdocs-minify-plugin`. Status: open, verified.
- F-31: `examples/README.md:31` `mexwell_request.rb` (→ `maxwell_request.rb`), `:50`
  `control_agent.rb` (→ `control.rb`). Status: open, verified.
- F-32: `sig/ai_agent.rbs` declares `module AiAgent` (old scaffold name). Rename to
  `sig/agent99.rbs`; #11's test asserts the old path ships, so do it after #11. Status: open, verified.
- F-33: CHANGELOG 0.0.5 both "Added SQLite-based registry" (`:18`) and "Removed SQLite
  registry" (`:37`). Edits released history. Status: needs Dewayne's decision.
- F-34: `README.md:181` → `docs/configuration/` (actual `docs/operations/configuration.md`);
  `README.md:266`, `CHANGELOG.md:42` → `docs/breaking_change_v0.0.4.md` (actual
  `docs/operations/breaking-changes.md`). Status: open, verified (fold in F-6's missing-example links).
- F-35: no `llms.txt`; sequence after F-26 so it doesn't spread the fictional API. Status:
  needs Dewayne's decision.
- F-36: neither listen loop rescues handler exceptions (`nats_message_client.rb:35-50`,
  `amqp_message_client.rb:63-79`); nats-pure swallows them, so errors vanish with no log.
  Status: open, verified (small PR after #13).
- F-37: `get(:field)` raises NameError when the class has no `REQUEST_SCHEMA`
  (`message_processing.rb:156-158`); no shipped example defines it. Status: issue
  [#14](https://github.com/MadBomber/agent99/issues/14).
- F-38: `examples/control.rb:84` does `payload[:data][:type]`, but pause/resume/shutdown/
  update_config acks send a String (`control_actions.rb:40,51,60,70`) → TypeError. Status:
  issue [#16](https://github.com/MadBomber/agent99/issues/16).
- F-39: `test/agent99/test_amqp_message_client.rb:38-53` builds `custom_config` and never uses
  it (same false-confidence pattern as F-2). Status: open, verified (improver).
- F-40: `lib/agent99/registry_client.rb:43` unused `response` assignment, the last `-w`
  warning in lib after #12. Status: open, verified (fold into another PR).
- F-41: `handle_control_response` (`control_actions.rb:20`) is unreachable: `process_control`
  routes `'response'` to `receive_response` (`message_processing.rb:58`). `CONTROL_HANDLERS` is
  documented public. Status: needs Dewayne's decision (delete vs rewire).
- S-6: security finding, details held locally. Status: open (MEDIUM if Pages re-enabled as-is).

### 2026-11-10 (fourth visit; quality audit + full dead-code pass + improver, develop 5183c92)

- F-42: `attr_reader :header` (`base.rb:41`) shadows `HeaderManagement#header`; `@header` set only in
  `process_request` (`message_processing.rb:38`), so header helpers in `receive_response`/`receive_control`
  return the previous request's header (NoMethodError before any request). Fix: drop the reader and the
  assignment; response-path test. Status: issue [#18](https://github.com/MadBomber/agent99/issues/18) (after #12; touches #17's question).
- F-43: `AmqpMessageClient#setup` ignores its `logger:` (`amqp_message_client.rb:43-50`); NATS assigns it.
  Status: issue [#20](https://github.com/MadBomber/agent99/issues/20).
- F-44: dispatch `case` and publish rescue ladder duplicated between AMQP and NATS clients (flay mass
  100/80/76 at `amqp:69,100,114` / `nats:41,70,78`). Shared `dispatch` is the fix point for F-36 and S-7.
  Status: open, verified (lib refactor after #13).
- F-45: constructor exits (`agent_lifecycle.rb:47,51`) and installs global `at_exit`/traps (`:101-107`),
  so `initialize` can't be tested in isolation (`test_agent_lifecycle.rb:45-48`). Status: needs Dewayne's
  decision (raise vs exit; opt-in signal handling).
- F-46: lifecycle tests set `error_logged`/`error_handled` and never assert them
  (`test_agent_lifecycle.rb:51,54,80,83,191`). Status: open, verified (tests-only PR).
- F-47: AMQP tests redefine `create_amqp_connection` without restoring in `ensure`
  (`test_amqp_message_client.rb:8-35,99-127`; `Lint/DuplicateMethods` :33,:239). Fold in F-39.
  Status: open, verified (tests-only PR).
- F-48: integration/system suites drive `TestInfrastructure::TestAgent`, which overrides `process_request`,
  `receive_request(payload, header)`, `send_request`, `discover_agents`, `get_all_agents`
  (`test/support/test_infrastructure.rb:254-361`); F-2 at suite scale. Status: needs Dewayne's decision (test strategy).
- F-49: no `.rubocop.yml`/`.reek.yml`; the "zero new offenses under the repo's config" taste rule can't be
  applied. Fix: `.rubocop.yml` (TargetRubyVersion 3.3) + generated todo. Status: open, verified.
- F-50: 14 unused assignments in tests (`test_registry_client.rb:48,82-84`, `test_amqp_message_client.rb:185`,
  `integration/test_agent_communication.rb:22,45,77,86-88,133,151,170,249`) + 12 unused `header` args in
  `system/test_full_workflows.rb`. Status: open, verified (tests-only PR).
- F-51: `RegistryClient#register` writes `@id` nothing reads (`registry_client.rb:23`), redundant
  `payload = info` (`:21`), wrong path in header comment (`:1`). Fold in F-40. Status: open, verified.
- F-52: unused public surface: `Agent99::Type` (`agent99.rb:15-19`), `AmqpMessageClient.instance` /
  `NatsMessageClient.instance`, `Timestamp#ts`, `Base::MESSAGE_TYPES` (test + one docs page only).
  Status: needs Dewayne's decision (public API; deprecation).
- F-53: `RegistryClient#handle_response` `JSON.parse`s any non-2xx body (`registry_client.rb:81`), so a 404/500
  logs as a JSON parse error and the status code is lost. Status: open, verified.
- F-54: failed registration leaves a running, unaddressable agent: `send_request` returns nil
  (`registry_client.rb:60-68`), `register` logs success with an empty ID (`agent_lifecycle.rb:61-62`), setup
  gets `agent_id: nil`, AMQP creates a server-named queue (`amqp_message_client.rb:53`). Status: issue
  [#19](https://github.com/MadBomber/agent99/issues/19) (improver).
- F-55: `handle_shutdown` calls `fini` then `exit(0)` (`control_actions.rb:41-42`); `at_exit { fini }`
  (`agent_lifecycle.rb:101`) runs it again and logs `WARN fini called with a nil id` on every remote
  shutdown. Fix: drop the explicit `fini` (check `exit` from a consumer thread ends the process) or make
  `fini` idempotent. Status: open, verified (improver).
- F-1 update: still dead at 5183c92. F-36 update: fix point is F-44. F-39 → fold into F-47. F-40 → fold into F-51.
- S-7: security finding, details held locally. Status: open.
- S-8: security finding, details held locally. Status: open (fixed by the #7 re-lock).

### 2026-11-10 (fifth visit; security audit re-run, develop 5183c92)

- S-1..S-7: re-confirmed open on develop 5183c92 (lib/ control plane byte-identical to the
  first pass; develop differs from main only by the #9 timestamp fix). Details held locally.
- S-8: fixed by the #7 re-lock (lock present/identical); but see S-9 for its vulnerable
  *contents*.
- S-9: security finding, details held locally. Status: open (vulnerable dependency chain in
  the committed lock, shipped via the `sinatra` runtime dep; `uri` CVE is on the core agent
  path). Overlaps #7 (lock re-resolution) and the dependencies audit; joint fix.
- S-10: security finding, details held locally. Status: open (`examples/` on `require_paths`
  executes a side-effecting script on `require`). Also closed by F-13's `require_paths = ["lib"]`
  change, after #11.
- S-11: security finding, details held locally. Status: open (LOW, unverified; unbounded TCP
  read + `symbolize_names` on untrusted JSON; concrete sink detail for S-3).
- F-7 → issue [#22](https://github.com/MadBomber/agent99/issues/22). F-23 → issue
  [#21](https://github.com/MadBomber/agent99/issues/21). F-27 → issue
  [#23](https://github.com/MadBomber/agent99/issues/23). All three filed this visit.
- Non-security this pass: none new. The gemspec file-list concern is already covered by the
  open `bomberbot/gem-files-allowlist` PR (#11); the `require_paths` residue is S-10 / F-13.
- Dead-code scan not re-run this visit; last covered commit stays 5183c92.

### 2026-11-10 (sixth visit; dependencies audit, develop 5183c92)

- F-56: a fresh `bundle install` resolves `bunny` 2.24.0 → **3.6.0** (major); the gemspec
  declares `add_dependency "bunny"` with no constraint (`agent99.gemspec:44`), so re-locking
  silently adopts an untested major. Suite passes but AMQP broker paths are mocked/skipped, so
  bunny 3.x is unexercised. Fix: pin the gemspec (`~> 2.24`, or validate 3.x and allow
  `">= 2.24", "< 4"`). Status: needs Dewayne's decision (supported bunny range).
- F-57: no CI runs `bundle install`/tests on any Ruby — `.github/workflows/` has only `docs.yml`
  (mkdocs). This is why the un-installable lock (#7) and the lock CVE drift sat unnoticed. Fix:
  add a Ruby matrix workflow (`bundle install` + `bundle exec rake test`, optionally
  `bundle-audit`). Status: issue [#26](https://github.com/MadBomber/agent99/issues/26).
- S-12: security finding, details held locally. Status: open (committed `Gemfile.lock` pins
  many known-vulnerable transitive gems — rack/concurrent-ruby/uri/rack-session/sinatra/json).
  Closed by the same #7 re-lock that fixes the install failure; the re-resolved lock is
  bundle-audit clean. Overlaps #7 and S-9.
- F-14 re-verified present at 5183c92: `logger` is `require`d in `base.rb:3` +
  the three message clients and exposed in public initializers, but `agent99.gemspec` declares
  no `logger` runtime dep (only transitive via sinatra); on Ruby 4.0 `logger` is a bundled,
  non-default gem. Status: issue [#25](https://github.com/MadBomber/agent99/issues/25).
- F-6 re-verified present at 5183c92: README Quick Start (`README.md:147-150`) + two getting-started
  docs teach overriding the private `process_request(payload)` dispatcher and `payload.dig(:name)`;
  the real hook is `receive_request` (no args) + `get(:name)` (examples). A follower's agent crashes
  on first request (nil `@payload`). Status: issue [#24](https://github.com/MadBomber/agent99/issues/24).
- F-1/F-51/F-53/F-55 re-verified still present at 5183c92 by the improver (no code change this
  visit); statuses unchanged.
- Dead-code scan not re-run this visit; last covered commit stays 5183c92.

### 2026-11-12 (seventh visit; dead-code audit, develop c2b4e49)

- Merged since last visit: #10 (#3), #11 (#5), #12 (#8), #13 (#6), #17 (F-2). Those issues are fixed on develop, open until release to main.
- F-58: `examples/registry.rb:4-5` requires `debug_me` + top-level `include DebugMe` but never calls it (LoadError without the dev gem); `:31` `agent_name` assigned, unused (`ruby -wc` warns). Status: PR'd [#29](https://github.com/MadBomber/agent99/pull/29).
- F-59: `lib/agent99/tcp_message_client.rb` is loaded by nothing (no `setup`/`delete_queue`, so it can't back `Base`), header says "not ready for prime time", ships via `lib/**/*`; lines 128-183 after `__END__` are a pasted AI-chat transcript; `header_schema.rb:17-19` has a stale line after `__END__`. Status: the `__END__` cleanup is open (comments only, small PR); removing or finishing the class needs Dewayne's decision (inbox).
- F-60: unused test helpers: `MockRegistryServer#reset` (`test/support/test_infrastructure.rb:44-46`), `TestAgent#clear_received_messages` (`:365-369`); lesser: `attr_reader :thread, :server` (`:9`), `MockQueue#messages` (`:228`). Status: open.
- F-61: CHANGELOG `[Unreleased]` has three near-duplicate #6 lines (`CHANGELOG.md:12-14`), left by #13's review-loop commits squashed together. Keep line 14. Status: open.
- F-62 (triage): `bin/console:5` `require "ai_agent"` → LoadError; pairs with F-32. Status: issue [#30](https://github.com/MadBomber/agent99/issues/30).
- F-63 (improver): six README Key Features anchors (`README.md:22-29`) match no heading. Status: issue [#31](https://github.com/MadBomber/agent99/issues/31).
- F-64 (improver): `Rakefile` `test_coverage`/`test_verbose` set env vars nothing reads; `simplecov` not a dependency. Status: issue [#32](https://github.com/MadBomber/agent99/issues/32).
- Re-checked at c2b4e49, still present: F-1 (`add_agent` is the only writer of `@agents`, so public `Base#agents` always returns `{}`; removing `agents` is public API → needs Dewayne's decision), F-40 (`registry_client.rb:43` unused `response`, the only `ruby -w` warning left in lib), F-41, F-51, F-52.
- #20 PR'd [#27](https://github.com/MadBomber/agent99/pull/27); #21 + #15 PR'd [#28](https://github.com/MadBomber/agent99/pull/28); #23 PR'd [#29](https://github.com/MadBomber/agent99/pull/29).
- Example registry also needs `rackup` (+ webrick/puma) under Sinatra 4; neither Gemfile nor gemspec lists it (noted by the #29 builder). Status: open, unverified as a docs vs dependency choice.

### 2026-11-12 (eighth visit; security audit, develop c2b4e49)

- Queue full (#27-#29 open, unapproved): no new PRs. develop unchanged at c2b4e49.
- F-65 (auditor): `RegistryClient` ignores the path of `AGENT99_REGISTRY_URL`: `registry_client.rb:14-17` stores `@base_url` (never read) and builds `Net::HTTP.new(uri.host, uri.port)`; requests go to absolute `/register`, `/discover`, `/` (`:22,36,42`), dropping any prefix. Fix: build request paths from the URL's path and configure the client from the whole URL; add a unit test. Status: open, PR candidate for the next visit.
- S-13: security finding, details held locally. Status: open.
- S-14: security finding, details held locally. Status: open.
- S-15: security finding, details held locally. Status: open.
- F-66 (triage + improver, reproduced by both): `bin/test` prints "All tests PASSED" and exits 0 when a test file fails; failed files never reach the totals (`bin/test:120-156`); glob also picks up `test/support/test_infrastructure.rb`. Status: issue [#33](https://github.com/MadBomber/agent99/issues/33).
- F-67 (improver): `test/README.md:41,47,50,53` glob `*_test.rb`; all test files are `test_*.rb`, so the commands run zero tests and exit 0. Status: issue [#34](https://github.com/MadBomber/agent99/issues/34).
- F-68 (improver): dev deps `mocha`, `hashdiff`, `amazing_print`, `tocer` (`agent99.gemspec:48,50-52`) have zero references (only a list in `docs/getting-started/installation.md:150-154`). Removing them and `bundle install` on Ruby 4.0.9 against the committed lock drops 29 gems, keeps bunny 2.24.0 (avoids F-56), only bumps minitest 5.25.5 -> 6.1.0 (+drb, prism) because 5.25.5 needs Ruby < 4; pinning `minitest ~> 5.25` gives 5.27.0, suite 72/0/0/4. A small path to #7 on Ruby 4.0 (not 4.1/rbtree, not S-12). Status: needs Dewayne's decision (minitest 6 vs `~> 5.25`; gemspec change).
- F-69 (improver): `validate_schema` (`message_processing.rb:130-138`) always returns `[]`; README "Schema Validation", `docs/framework-components/message-processing.md`, `docs/api-reference/schemas.md:47,175` (`HeaderSchema.new.validate!`, nonexistent) promise validation. Status: needs Dewayne's decision (add a validator dep such as json_schemer, or remove the stub and the claims).
- F-70 (triage): a plain #7 re-lock also jumps `json` 2.13.2 -> 3.1.0 (major, via nats-pure/sinatra) and, with the local default bundler, writes `bundler 4.1.0.beta3` into the lock; pin a stable bundler. Extends F-56. Status: needs Dewayne's decision (with F-56).
- Minor (improver): `examples/control.rb:127` unused `statuses`; three suppressed exceptions in `run_demo.rb`. Fold into the next examples PR.
- RubyGems baseline (triage): 0.0.5 latest (2025-09-03); 2,938 total downloads, 668 for 0.0.5; no reverse deps. Dependabot alerts disabled (403); discussions off.
- Re-checked: #30 (bin/console), #14, #18, #22, #32, F-40 still present.


### 2026-11-12 (ninth visit; quality audit re-run, develop c2b4e49)

- Queue full (#27-#29 open, unapproved): no new PRs. develop unchanged at c2b4e49.
- Metrics at c2b4e49: flog 631.5, flay 256 (same AMQP/NATS duplicates), reek 83, rubocop 249 lib / 878 test; `ruby -wc` one warning (`registry_client.rb:43`, F-40). Suite in a `git archive` export: 72 runs, 4 failures (all F-75), 4 skips.
- F-71 (auditor): `RegistryClient` can't talk to an `https://` registry URL: `registry_client.rb:16-17` builds `Net::HTTP.new(uri.host, uri.port)` and never sets `use_ssl` (probe: port 443, `use_ssl?` false). Fix with F-65 (configure the client from the whole URL), unit test for each. Status: open, PR candidate bundled with F-65.
- F-72 (auditor + improver, reproduced by main session): `discover_agent` raises `NoMethodError` (`empty?` for nil) instead of the documented "No agents available" when the registry is unreachable (`agent_discovery.rb:28`, `registry_client.rb:60-68`). Status: issue [#35](https://github.com/MadBomber/agent99/issues/35).
- F-73 (auditor + improver): `attr_reader :capabilities` (`base.rb:40`) shadows `AgentDiscovery#capabilities` (`agent_discovery.rb:9-11`), so `@capabilities = capabilities` (`agent_lifecycle.rb:17`) assigns nil and `Base#capabilities` is always nil for agents that declare capabilities in `info`; the module's `[]` default is unreachable; tests pass only because `TestInfrastructure::TestAgent` sets the ivar by hand (`test_infrastructure.rb:264`). Same pattern as F-42. Status: needs Dewayne's decision (what public `Base#capabilities` should return: `info[:capabilities]`, `[]`, or be removed).
- F-74 (auditor + improver, reproduced): `get(field)` (`message_processing.rb:146-148`) uses `||`, so an explicit `false` (or nil) in the request is replaced by the schema example (`loud: false` → `true`). Status: issue [#36](https://github.com/MadBomber/agent99/issues/36); pair with #14 in one PR.
- F-75 (auditor, reproduced): the four file-list tests in `test/agent99/test_gemspec.rb` fail outside a git checkout (`git ls-files` empty → `spec.files == []`). Fix: skip with a message when not in a work tree. Tests only; wait for F-13, which renames `:34`. Status: issue [#37](https://github.com/MadBomber/agent99/issues/37).
- F-76 (improver): `header_management.rb:7` has `# private` commented out, so `type`, `to_uuid`, `from_uuid`, `event_uuid`, `timestamp`, `return_address` are public on every agent. Status: needs Dewayne's decision (restore `private` with a CHANGELOG note, document as public, or deprecate for one release).
- F-44 update: both listen loops raise `NotImplementedError` for an unknown `header.type` (`amqp_message_client.rb:77`, `nats_message_client.rb:49`); it is a `ScriptError`, so a `rescue StandardError` fix for F-36 would still let it escape. Log and drop unknown types instead. Also: redundant `Bunny::TCPConnectionFailed` alongside `StandardError` (`amqp_message_client.rb:144`).
- Re-checked still present: F-40, F-41, F-42, F-43 (PR #27), F-44, F-45, F-46, F-47, F-49, F-50, F-51, F-53, F-55; #14, #22, #25, #30, #32, #33, #34.


### 2026-11-13 (tenth visit; adoption audit re-run + dead-code delta, develop 28a6347)

- Since last visit: #27–#29 squash-merged (develop 28a6347). Dewayne opened #38–#54, one per open issue (#7, #14, #16, #18, #19, #22, #24–#26, #30–#37). Responder merged all 17 in scratch: only CHANGELOG conflicts; suite 84/0/0/4 on 4.0.9 and 4.1-preview1 with #53's lock.
- F-55: **PR open** [#55](https://github.com/MadBomber/agent99/pull/55). F-65 + F-71: **PR open** [#56](https://github.com/MadBomber/agent99/pull/56). F-59 (notes after `__END__`): **PR open** [#57](https://github.com/MadBomber/agent99/pull/57).
- F-77 (auditor): docs show 0.0.4 as current (`docs/getting-started/overview.md:7`), `docs/index.md:79` dates 0.0.4 2024-12-12 (actually 12-14) and has no 0.0.5; CHANGELOG `[0.0.1]` date 2024-11-28 vs RubyGems/tag 2024-12-05 (released history: Dewayne's call, like F-33). Status: issue [#59](https://github.com/MadBomber/agent99/issues/59).
- F-78 (auditor, verified): example registry (README Quick Start step 3) won't start: Sinatra 4 needs `rackup`, listed nowhere; with `rackup` added it runs on WEBrick and #29's fix works. Docs at `quick-start.md:44`, `installation.md:114`, `basic-example.md:206`, `examples/README.md:131`. Fix touches `Gemfile.lock` (after #53) and ties to F-12. Status: needs Dewayne's decision (Gemfile dev group, gemspec, or docs only).
- F-79 (auditor): docs toolchain unpinned (`docs.yml` `pip install mkdocs-material mkdocs-minify-plugin`, `setup-python@v4`); Material for MkDocs EOL 2027-05-05 (Zensical successor). Status: needs Dewayne's decision (likely cross-repo).
- F-90 (improver, timed): `MockRegistryServer#start` `sleep 0.5` (`test/support/test_infrastructure.rb:24-25`) runs before every test; suite 38.2 s → ~1.5 s without it, 74/0/0/4. Status: issue [#58](https://github.com/MadBomber/agent99/issues/58).
- F-91 (improver, reproduced): `TcpMessageClient#publish` calls `agents(target)` (`tcp_message_client.rb:40`) on a no-arg reader → `ArgumentError` on every publish. Nothing loads the file. Status: needs Dewayne's decision (with F-59 remainder: delete, fix, or exclude from the gem).
- F-92 (improver): nothing in `test/` exercises `handle_pause/resume/update_config/status_request` or `CONTROL_HANDLERS`; `TestAgent` replaces `process_control`. Status: issue [#60](https://github.com/MadBomber/agent99/issues/60).
- S-16: security finding, details held locally. Status: fix in PR #56.
- Minor: `examples/chief_agent.rb:50` `rescue Exception => e`, `e` unused (fold into next examples PR); `test_amqp_message_client.rb` redefines `create_amqp_connection` (method-redefinition warning); signal handler uses `STDERR.puts` (`agent_lifecycle.rb`, in #42).
- Review notes on Dewayne's PRs (inbox): #51 misses the archive-inside-another-repo case; #54's Rakefile comment overstates a failure; #45 leaves 48 `def process_request(payload)` overrides in 10 other docs pages (F-26).
- Re-checked still present: F-1, F-6 (missing example links), F-8, F-20, F-28, F-30, F-31, F-32, F-34, F-40, F-51, F-53, F-60, F-61.


### 2026-11-13 (eleventh visit; dependencies audit, develop 28a6347)

- F-93 (auditor, reproduced): `bin/console` still fails on Ruby 4.0+ after #47: `require "irb"` raises `cannot load such file -- irb` under `bundler/setup` (irb is a bundled gem in 4.0, not in Gemfile/lock). Reproduced on 4.0.9 and 4.1.0-preview1 with #47 on #53+#46. Fix: `gem "irb"` in Gemfile's dev group + re-lock, after #53. Status: issue #63.
- F-94 (auditor): after #53 the committed lock needs Ruby >= 4.0 (15 tocer-chain gems declare `>= 4.0`) while the gemspec floor is `>= 3.3.0`; #54 installs unfrozen, so its 3.3/3.4 legs test a different bundle. Dropping unused dev deps (F-68) would let one lock serve 3.3–4.1. Status: needs Dewayne's decision (with F-68).
- S-17: security finding, details held locally. Status: open.
- F-100 (improver, reproduced): `agent_lifecycle.rb:30,38` use `respond_to?` without `include_all`, so a private `info` gives a false "must implement info" error and a private `init` is silently skipped; string-key `info` reports present keys as missing. Fix: `respond_to?(x, true)` + tests, after #42. Status: issue #61.
- F-101 (improver, reproduced): `docs/advanced-topics/control-actions.md:124-129` snippet `CONTROL_HANDLERS.merge!` raises NameError inside an agent class (constant is `Agent99::CONTROL_HANDLERS`), and the merge is process-global. Status: issue #62.
- F-102 (improver, reproduced): pause drops requests (`message_processing.rb:25` `process_request(message) unless paused?`: consumed, no reply, no log, no replay), while `control-actions.md:33-37,55-58` says they are queued and replayed. Status: needs Dewayne's decision (pause semantics; taste says change the code, not the docs). Inbox.
- F-103 (improver): `lib/agent99/.irbrc` is a 5-line leftover from the AiAgent rename; fold into the next lib cleanup. Status: open.
- Re-confirmed at 28a6347: #7/F-10 (#53 sufficient for install; rbtree 0.4.7 clears F-11), F-14 (#46 sufficient), F-56 (still unconstrained), F-57 (#54 fixes; gaps per F-94), S-12 (#53 does not close it; see S-17).


### 2026-11-13 (twelfth visit; quality audit re-run, develop 28a6347)

- F-104 (auditor; main session reproduced it with a real `Concurrent::ThreadPoolExecutor`): a remote `shutdown` doesn't end a NATS agent. nats-pure 2.7.0 runs callbacks on a concurrent-ruby 1.3.8 pool worker whose `run_task` rescues `Exception`, so `exit(0)` in `handle_shutdown` (`control_actions.rb:42`) ends only the worker (`ERROR -- exit: nil`). On develop the agent withdraws and keeps running unregistered. With #55's first commit it stayed registered. AMQP works only because bunny's `subscribe(block: true)` joins the pool thread. Status: fixed in **PR open** [#55](https://github.com/MadBomber/agent99/pull/55) (9368c76: `Thread.main.raise(SystemExit.new(0))`, plus a worker-thread test that fails before the change).
- F-105 (improver, reproduced): five doc lines name `REGISTRY_BASE_URL` (`docs/operations/configuration.md:7,15`, `docs/framework-components/agent-discovery.md:55`, `docs/api-reference/overview.md:100`, `docs/agent99_framework/registry_client.md:12`); the code reads `AGENT99_REGISTRY_URL`, so the documented variable is silently ignored. Status: issue [#64](https://github.com/MadBomber/agent99/issues/64).
- F-106 (improver, reproduced): `examples/run_demo.rb:348` generates `examples/agents/demo_agent*.rb` with `require_relative '../lib/agent99'` → LoadError; should be `../../lib`. Status: issue [#65](https://github.com/MadBomber/agent99/issues/65).
- F-107 (improver, reproduced with Rack::MockRequest): the example registry answers missing or invalid input (`GET /discover` without `capability`, `POST /register` without or with a string `capabilities`, a non-JSON body) with a 500 HTML page; the client then hides it (F-53). Overlaps S-4; check the scope there first. Status: open.
- F-108 (improver, verified): `docs/agent99_framework/registry_client.md:26` `register(name:, capabilities:)` (the code takes `info:`), and `message_client.md:36,85,98` show the wrong `initialize`/`publish`/`delete_queue` signatures. These are real methods, so it's separate from F-26. Status: issue [#66](https://github.com/MadBomber/agent99/issues/66).
- Inbox: `docs/AI/` is unrelated HTM material (~100 KB including a SQLite `.db`) in the mkdocs nav; `docs/api-reference/{message-clients,registry-client}.md` add more invented API (evidence for F-26); `dispatcher` resets `@config`/`@paused` (`message_processing.rb:19-20`), so config set in `init` is lost. Needs Dewayne's decision.
- Minor: the `validate_info_keys` heredoc (`agent_lifecycle.rb:42-43`) relies on trailing spaces; fold into #42's follow-up. `examples/kaos_spy.rb:29` calls `agents.empty?` on nil when the registry is down.
- Security: the auditor's S-18 is S-7 (it adds the NATS detail; held locally); the improver's example-shell note is S-13. No new S numbers.
- Re-checked still present: F-40, F-41, F-42, F-44, F-51, F-53, F-73, F-76, F-101.


### 2026-11-13 (thirteenth visit; security audit, develop 28a6347)

- Security audit, fourth run. bundle-audit 0.9.3, DB 4d71b12 (upstream HEAD): committed lock 31 advisories (unchanged); #53's lock (f09c47c) 28 (unchanged); fresh re-resolve clean. lib delta since c2b4e49 is #27/#28/#29 only; full lib re-read.
- S-19: security finding, details held locally. Status: needs Dewayne's decision (with S-1, S-2).
- S-20: security finding, details held locally. Status: open (small lib PR once the queue clears).
- S-21: security finding, details held locally. Status: needs Dewayne's decision.
- F-109 (improver, verified with `ruby -c`): docs Ruby samples that don't run: `docs/framework-components/agent-registry.md:25-64` irb walkthrough is a SyntaxError (missing comma `:44`, pasted log/inspect lines `:55-60`); `on_exit { fini }` in `agent-registry.md:91`, `agent-lifecycle.md:69,135` (code is `at_exit`); `what-is-an-agent.md:245-257` missing `end`; shell in a ruby fence `advanced-features.md:88-90`; nonexistent `deregister_from_registry` `agent-lifecycle.md:85-91`. Status: issue [#67](https://github.com/MadBomber/agent99/issues/67).
- F-110 (improver): ~12 misspellings in `breaking-changes.md`, `agent-lifecycle.md:77`, `agent-registry.md` (incl. `RegristryClient`, `@agnet_1..3` in code), `examples/start_rabbitmq_and_registry.sh:6,15`. Status: issue [#68](https://github.com/MadBomber/agent99/issues/68).
- F-111 (improver, reproduced): `info` without `name` exits 1 (`agent_lifecycle.rb:37` requires `[:name, :capabilities]`) while `breaking-changes.md:15-18` says name is derived from the class name and that `type`/`request_schema` are required. Code fix (default to class name) after #42. Status: issue [#69](https://github.com/MadBomber/agent99/issues/69).
- F-112 (improver, minor): `examples/diagram.png` (51.5 KB) could be an SVG from `examples/diagram.dot` (5.8 KB) with a dark/transparent style. Status: open, low value.
- Updates: F-20 also `docs/agent-development/error-handling-and-logging.md:10` (`LOG_LEVEL` read by nothing); F-29 also `examples/README.md:155` ("fallback implementations").
- Re-confirmed open at 28a6347: S-1..S-15, S-17 (S-16 fix still in PR #56).

### 2026-11-13 (fourteenth visit; dead-code full pass + improver, develop 28a6347)

- F-113 (auditor): unused public API: `AmqpMessageClient` `attr_accessor :logger, :channel, :exchange` (`amqp_message_client.rb:31`; `channel` reader and `channel=`/`exchange=` writers have no caller), `NatsMessageClient` `attr_accessor :logger, :nats` (`nats_message_client.rb:16`; `nats`/`nats=` unused), `Timestamp#utc` alias (`timestamp.rb:22`). Zero hits in lib, examples, test, docs and `agent99_examples`. Status: **needs Dewayne's decision** (public API; group with F-52).
- F-114 (auditor + improver, verified): 10 of 13 tests in `test/agent99/test_amqp_message_client.rb` exercise `TestInfrastructure::MockAmqpMessageClient` (set in `test_helper.rb:15-20`), not the real class; mutants in real `publish`/`delete_queue`/`create_queue`/subscribe caught by one test only. Status: issue #71.
- F-115 (auditor, verified): `require 'simple_json_schema_builder'` dead in `base.rb:5` and `amqp_message_client.rb:5` (only `header_schema.rb` uses it and requires it itself); removal probe green. Status: open, ride along with the next lib PR touching those files (base.rb off limits while #38–#54 open).
- F-116 (improver, verified): `NatsMessageClient#publish`, the `listen_for_messages` dispatch and `delete_queue` have no tests (only two `initialize` failure tests). Status: issue #72.
- F-117 (improver, reproduced): `ruby examples/maxwell_request.rb` exits 1 with `NameError: uninitialized constant Agent99` (line 3 requires only `header_schema`). One-line fix: `require_relative '../lib/agent99'`. Status: issue #70.
- F-118 (improver, minor): stale `# FIXME: message.to_json` at `amqp_message_client.rb:88`; `.envrc:1` header says `# ai_agent/.envrc` (rename leftover like F-32/F-103). Status: open, ride along.
- Updates: F-69 also: the `rescue` in `validate_schema` (`message_processing.rb:134-137`) is unreachable (bare `return []`). F-60 also: mock `channel`/`exchange` accessors (`test_infrastructure.rb:169`) unused. Minor examples: `AgentWatcher::TYPE` (`agent_watcher.rb:29`) and `Control#statuses=` (`control.rb:23`) unused.
- Improver's `chief_agent.rb:96` shell-out note = S-13 (already held locally).
- Re-confirmed present at 28a6347: F-1, F-40, F-41, F-42/F-73, F-51, F-52, F-59/F-91, F-60, F-103.
