# Findings: agent99

Running backlog of audit findings. Append under a dated heading, never replace. Keep
every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs
Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-09 | First run, at main 69f0a04 (v0.0.5; the repo has no `develop`). No eval/shell-out/Marshal/YAML.load in `lib/`; all wire data is JSON via `JSON.parse(symbolize_names: true)`. Exposure is missing auth, disabled validation, unbounded input: S-1..S-5 (held locally). Found non-security F-1 (dead method) and F-2 (test asserts a double). |
| dead code | 2026-11-09 (lib, brief) | During the security pass. One true dead method, F-1 (`AgentDiscovery#add_agent`). `lib/agent99/tcp_message_client.rb` is never required by the library (base.rb loads only amqp + nats) — removal is a public-surface decision, in the inbox. |
| dependencies | 2026-11-09 | First run, develop 69f0a04. Committed lock doesn't install as-is on Ruby 4.0.9 (tocer 19 chain pins Ruby ~> 3.4; BUNDLED WITH 2.5.23); re-resolved, suite green 57/0/0/4 skips. Ruby 4.1-preview1: rbtree 0.4.6 build failure (0.4.7 fixes). F-10..F-17. No removed-API use in `lib/`. Floor `>= 3.3` OK until 3.3 EOL (2027-03). bunny 3.x untested. Improver added F-18..F-20. |
| adoption | 2026-11-10 | First run, develop 5183c92. Docs site 404 (Pages off; gh-pages last deployed 2025-09-03). README agent fails on load (HeaderSchema not loaded, F-23) then crashes silently on every request (F-6); example registry needs rackup and 500s on WEBrick (F-27); API reference documents 13 nonexistent methods (F-26); `mkdocs build --strict` passes. F-23..F-36, S-6; updated F-6/8/9/20/21/22. Improver added F-37, F-38. gem-skill not run (paid API). |
| quality | n/a | Not yet run. No `.rubocop.yml`/`.reek.yml` in the repo. Suite emits many unused-variable warnings. |

Dead-code scan last covered commit: 69f0a04 (no dead-code audit this visit; improver re-verified F-1 at 5183c92) (develop, created from main 2026-11-09)

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
  code, dead on arrival on the error path. Status: open, verified.
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
  after #10. Status: open, verified (top PR candidate next visit).
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
  Rack 3 input. Works on Puma or with line 29 deleted. Status: open, verified (one-line PR
  candidate; Gemfile part with #7).
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
