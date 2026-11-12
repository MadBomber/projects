# Findings: agent99

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-12 | Third run, develop c2b4e49. bundle-audit 0.9.3, DB 4d71b12: committed lock 31 advisories; re-resolved lock clean. S-1..S-12 re-confirmed open. New F-65, S-13..S-15 (held locally). Earlier: 2026-11-10 develop 5183c92 (S-9..S-11), 2026-11-09 main 69f0a04. |
| dead code | 2026-11-12 (full) | debride 1.15.2 on lib+examples+test at develop c2b4e49, hand-verified; dependents checked (`agent99_examples` pins 0.0.4, uses none of the candidates; the earlier "no dependent repos" note was wrong). New F-58..F-61; triage/improver added F-62..F-64. F-1 (plus public `Base#agents` question), F-40, F-41, F-51, F-52 still present. Live: `handle_*` via `send`, `HeaderManagement#type` (#17 asserts it public), client setters (tests), `Agent99.version` (docs). |
| dependencies | 2026-11-10 | Second run, develop 5183c92. Committed lock still un-installable on Ruby 4.0.9 (`core 2.3.0` pins ruby ~> 3.4) and 4.1-preview1; re-resolve succeeds on both, suite green 64/0/0/4 skips. `rbtree` re-resolves to 0.4.7 (clears the 4.1 build failure). Committed lock carries ~34 bundle-audit advisories (held locally as S-12; the re-resolved lock is clean). New F-56 (re-lock jumps bunny 2.24→3.6 major, unguarded by gemspec; needs-decision), F-57 (no Ruby CI). Re-verified present: F-14 (logger undeclared), #7 (lock un-installable/stale). All three (S-12, #7 install, Note #7) close with one re-lock. First run was 2026-11-09, develop 69f0a04: F-10..F-17, improver F-18..F-20. |
| adoption | 2026-11-10 | First run, develop 5183c92. Docs site 404 (Pages off; gh-pages last deployed 2025-09-03). README agent fails on load (HeaderSchema not loaded, F-23) then crashes silently on every request (F-6); example registry needs rackup and 500s on WEBrick (F-27); API reference documents 13 nonexistent methods (F-26); `mkdocs build --strict` passes. F-23..F-36, S-6; updated F-6/8/9/20/21/22. Improver added F-37, F-38. gem-skill not run (paid API). |
| quality | 2026-11-12 | Second run, develop c2b4e49. flog 631.5 (avg 7.6), flay 256, reek 83, rubocop 249 lib / 878 test (defaults + NewCops, no repo config). New F-71..F-75 (improver independently reproduced F-72..F-74, added F-76); F-44 updated. F-40..F-47, F-49..F-51, F-53, F-55 still present. First run 2026-11-10, develop 5183c92 (flog 640.8, reek 83, rubocop 249/857). |

Dead-code scan last covered commit: c2b4e49 (develop, full pass 2026-11-12)

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
