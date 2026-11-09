# Findings: simple_flow

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-09 | First run (first visit, which was cut off before it wrote brain), at develop 915656e. The released gem has zero runtime dependencies, no shell-outs, no deserialization, no regexps in lib. S-1, S-2 held locally. |
| dead code | 2026-11-09 (lib only, brief) | develop e8196cc. Nothing dead in lib. `DependencyGraph#subgraph`/`#merge`/`#reverse_order` are called only from tests but are public API (F-12). Generated `pipeline_graph.*` files tracked at the repo root (F-8). |
| dependencies | n/a | Not yet run. Triage noted: Dependabot alerts disabled; dev-group minor bumps available (async 2.46, rubocop 1.92); minitest 6 and simplecov 1.x are majors. |
| adoption | n/a | Not yet run. Partly covered: #10 (README accuracy), F-11 (`documentation_uri`), no `llms.txt`. |
| quality | 2026-11-09 | First run, at develop e8196cc, Ruby 4.0.9. Suite 193 runs, 0 failures, 2 skips. Found F-1..F-7, mostly `call_parallel` silently skipping steps. |

Dead-code scan last covered commit: e8196cc (develop, 2026-11-09)

## Findings

### 2026-11-09 (first visit, cut off before recording; security audit, develop 915656e)

- S-1: security finding, details held locally. Status: needs Dewayne's decision.
- S-2: security finding, details held locally. Status: open (informational).

### 2026-11-09 (second visit; quality audit + brief dead-code pass, develop e8196cc)

- F-1: `call_parallel` (`strategy: :auto`) silently skips a step whose `depends_on:` names an unknown step; `call` runs it. `pipeline.rb:111` stores deps unvalidated; the graph loop ends with no error. Re-reproduced by the main session. Fix: raise `ArgumentError` at the start of `execute_with_dependency_graph`. Status: issue [#12](https://github.com/MadBomber/simple_flow/issues/12).
- F-2: `call_parallel` (`:auto`) drops every unnamed step once any named step exists (`pipeline.rb:209`, `:332-403`). Status: needs Dewayne's decision (raise on mixed pipelines vs fall back to explicit parallelism).
- F-3: Named steps inside an unnamed `parallel do` block never run under `call_parallel` (`pipeline.rb:151-163` writes `@step_dependencies` only for named groups). Re-reproduced by the main session. Status: issue [#13](https://github.com/MadBomber/simple_flow/issues/13).
- F-4: `depends_on:` a parallel group declared later is not expanded (`expand_dependencies` runs at declaration time, `pipeline.rb:277-287`), so the dependent step never runs. Fix: expand at run time. Status: open (medium-sized; after #12).
- F-5: Duplicate step names overwrite in `@named_steps` (so `call_parallel` runs only the second) but both run in `call` (`pipeline.rb:103`, `:114`, `:487`). Fix: raise on a duplicate name. Status: open (behaviour change; issue first).
- F-6: `ParallelBlock#step` ignores `depends_on:` and skips the reserved-name check (`pipeline.rb:480-488`). Status: open for the reserved-name check; the `depends_on:` keyword needs Dewayne's decision.
- F-7: `test/test_simple_flow.rb` never runs under `rake test` (Rakefile pattern `test/**/*_test.rb`); it holds the only VERSION assertion. Status: PR'd [#17](https://github.com/MadBomber/simple_flow/pull/17).
- F-8: Generated `pipeline_graph.dot/.html/.mmd` tracked at the repo root since 2b1dfdd; example 09 writes them to the CWD and the root `.gitignore` doesn't cover them. Status: PR'd [#15](https://github.com/MadBomber/simple_flow/pull/15).
- F-9: `MiddleWare::Logging` crashes with the documented `use_middleware ..., logger: x` (`middleware.rb:4` takes it positionally; `apply_middleware` passes `**options`), raising `NoMethodError: undefined method 'info' for an instance of Hash`. Status: PR'd [#16](https://github.com/MadBomber/simple_flow/pull/16).
- F-10: Example regression baselines in `examples/regression_test/` are stale since the 0.4.0 parallel-merge fix (05, 06, 07, 10, 12 differ; 13 has none). Status: issue [#14](https://github.com/MadBomber/simple_flow/issues/14).
- F-11: `documentation_uri` points at the README on GitHub, not the docs site; the gemspec keeps bundler template comments and `bindir = "exe"` with no `exe/`. Redundant `require_relative 'dependency_graph'` at `pipeline.rb:333`. Status: open (small adoption PR candidate).
- F-12: `DependencyGraph#subgraph`, `#merge`, `#reverse_order` are called only from tests. Public API, so not dead. Status: needs Dewayne's decision (keep or deprecate).
- F-13: `ASYNC_AVAILABLE` is a top-level constant on `Object` (`parallel_executor.rb:4-9`). Status: needs Dewayne's decision (namespace with a deprecated alias, replace with a predicate, or leave).
- F-14: `MiddleWare::Instrumentation` writes with `puts` and has no logger/IO option (`middleware.rb:30-31`). Status: needs Dewayne's decision (changes output).
- F-15: `use_middleware` only wraps steps declared after it; middleware declared after the steps never runs. Status: needs Dewayne's decision (apply lazily vs warn).
- F-16: API docs pages show an absolute local checkout path as each class's **Location** (`docs/api/pipeline.md:7`, `middleware.md:9,55`, `result.md:7`, `parallel-step.md:7,121`, and four guide pages). Fix: repo-relative paths. Status: open (small docs PR candidate).
- F-17: Ruby floor `>= 3.2.0` includes EOL 3.2, and the test CI matrix already skips 3.2 (io-event needs 3.3). Status: open (floor bump to 3.3; taste rule debug_me#23).
- F-18: `StepTracker` isn't loaded by `require "simple_flow"`; the error-handling guide says to require it explicitly, so this is intended. Its class doc example uses nonexistent `with_continue`/`.continue`. Status: open (fix the doc comment).
