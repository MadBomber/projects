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
| dependencies | n/a | Not yet run. Triage noted no `dependabot.yml`; safe minors behind (nats-pure, sinatra, rack, dry-schema, debug_me); several majors out of scope (bunny 2→3, json 2→3, mocha 2→3). |
| adoption | n/a | Not yet run. Covered in part: gem bloat (issue #5), no `llms.txt`, no `documentation_uri`, misleading gemspec description, README/example drift. |
| quality | n/a | Not yet run. No `.rubocop.yml`/`.reek.yml` in the repo. Suite emits many unused-variable warnings. |

Dead-code scan last covered commit: 69f0a04 (main, 2026-11-09; no develop branch exists)

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
  Status: issue [#3](https://github.com/MadBomber/agent99/issues/3).
- F-4: `Agent99::Timestamp` round-trip is broken. Encode `timestamp.rb:27`
  (`now.to_i * 1_000_000 + now.usec`, decimal) vs decode `timestamp.rb:32`
  (`Time.at(microseconds >> 20, microseconds & 0xFFFFF)`, binary). Reproduced: a 2026
  time round-trips to 2024 (>2-year drift). No test covers `ts2utc`. Fix:
  `Time.at(microseconds / 1_000_000, microseconds % 1_000_000).utc`. Status: issue
  [#4](https://github.com/MadBomber/agent99/issues/4).
- F-5: published gem is ~1.5 MB. `spec.files` (`agent99.gemspec:35-41`) uses `git ls-files`
  minus a tiny denylist, so docs images (~1.3 MB logo PNG), a binary `docs/AI/htm_demo.db`,
  planning notes (`A2A_SPEC-dev.md`, `p2p_plan.md`, `p2p_roadmap.md`, `registry_plan.md`),
  `COMMITS.md`, `DOCS.md`, `Rakefile`, `mkdocs.yml`, and `lib/agent99/.irbrc` all ship.
  Reproduced via `gem build` (1,568,256 bytes). Fix: allowlist `spec.files`. Status:
  issue [#5](https://github.com/MadBomber/agent99/issues/5).
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
  description rewrite is content → inbox. Status: open, verified.
- F-9: CHANGELOG `[Unreleased]` (`CHANGELOG.md:3-4`) is empty despite two post-0.0.5
  commits (`69f0a04` test, `0db4210` docs). Minor discipline gap. Status: open, verified.
