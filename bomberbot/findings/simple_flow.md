# Findings: simple_flow

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-09 | First run (first visit, which was cut off before it wrote brain), at develop 915656e. The released gem has zero runtime dependencies, no shell-outs, no deserialization, no regexps in lib. S-1, S-2 held locally. |
| dead code | 2026-11-09 (lib only, brief) | develop e8196cc. Nothing dead in lib. `DependencyGraph#subgraph`/`#merge`/`#reverse_order` are called only from tests but are public API (F-12). Generated `pipeline_graph.*` files tracked at the repo root (F-8). |
| dependencies | n/a | Not yet run. See F-19 (logger/tsort runtime deps) from the 2026-11-10 adoption audit. Triage noted: Dependabot alerts disabled; dev-group minor bumps available (async 2.46, rubocop 1.92); minitest 6 and simplecov 1.x are majors. |
| adoption | 2026-11-10 | First run, at develop f58e221, Ruby 4.0.9. 13/13 examples exit 0; README snippets run with stubs; `mkdocs build --strict` passes (INFO: dagwood-concepts.md not in nav; Material for MkDocs EOL May 2027); CHANGELOG complete for v0.1.0-v0.4.0; no llms.txt. Found F-19..F-27; F-19 (gem won't load on Ruby 4.0 under Bundler) also covers the Ruby 4 part of a dependencies audit. |
| quality | 2026-11-09 | First run, at develop e8196cc, Ruby 4.0.9. Suite 193 runs, 0 failures, 2 skips. Found F-1..F-7, mostly `call_parallel` silently skipping steps. |

Dead-code scan last covered commit: e8196cc (develop, 2026-11-09)

## Findings

### 2026-11-09 (first visit, cut off before recording; security audit, develop 915656e)

- S-1: security finding, details held locally. Status: needs Dewayne's decision.
- S-2: security finding, details held locally. Status: open (informational).

### 2026-11-09 (second visit; quality audit + brief dead-code pass, develop e8196cc)

- F-1: `call_parallel` (`strategy: :auto`) silently skips a step whose `depends_on:` names an unknown step; `call` runs it. `pipeline.rb:111` stores deps unvalidated; the graph loop ends with no error. Re-reproduced by the main session. Fix: raise `ArgumentError` at the start of `execute_with_dependency_graph`. Status: issue [#12](https://github.com/MadBomber/simple_flow/issues/12), PR'd [#21](https://github.com/MadBomber/simple_flow/pull/21).
- F-2: `call_parallel` (`:auto`) drops every unnamed step once any named step exists (`pipeline.rb:209`, `:332-403`). Status: needs Dewayne's decision (raise on mixed pipelines vs fall back to explicit parallelism).
- F-3: Named steps inside an unnamed `parallel do` block never run under `call_parallel` (`pipeline.rb:151-163` writes `@step_dependencies` only for named groups). Re-reproduced by the main session. Status: issue [#13](https://github.com/MadBomber/simple_flow/issues/13), PR'd [#21](https://github.com/MadBomber/simple_flow/pull/21).
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

### 2026-11-10 (third visit; adoption audit, develop f58e221)

- F-19: On Ruby 4.0, `require "simple_flow"` raises `LoadError` in any Bundler app that doesn't list `logger` itself (`lib/simple_flow.rb:13`; the gemspec declares no runtime deps; `logger` left the default gems in 4.0.0). `tsort` (`dependency_graph.rb:3`) follows in 4.1.0. Hidden by the dev-group `logger` in the Gemfile and the 3.3/3.4-only CI. Reproduced with a consumer app on 4.0.9. Status: PR'd [#22](https://github.com/MadBomber/simple_flow/pull/22).
- F-20: Docs-site pages say the no-async fallback is sequential (`docs/concurrent/performance.md:7,34-42`, `docs/api/parallel-step.md:9,81`, `docs/api/pipeline.md:269`, `docs/getting-started/examples.md:400`); the code uses threads. Status: issue [#19](https://github.com/MadBomber/simple_flow/issues/19).
- F-21: `docs/api/parallel-step.md:55-68` documents `ParallelExecutor.execute_sequential`, which was replaced by `execute_with_threads` in 13820c4 before v0.1.0 and is in no release. The improver's history check settles it as a docs error, not a missing method. Status: issue [#19](https://github.com/MadBomber/simple_flow/issues/19) (folded in with F-20).
- F-22: `docs/getting-started/installation.md` (Ruby `>= 2.7.0`, `async` "automatically installed") and `docs/development/testing.md` (121 tests, CI on 2.7-3.3 for every push) are stale. Status: issue [#20](https://github.com/MadBomber/simple_flow/issues/20).
- F-23: `docs/api/pipeline.md` omits `concurrency:`, named `parallel :name, depends_on:` groups, and the `concurrency`/`parallel_groups` readers; typo `:concurrency: :async` at :171. Named groups appear nowhere in the README, docs or examples. Status: open (document after #13 lands).
- F-24: README nits: hard-coded test counts (`README.md:5`, `:484-485`), the old `{:validation=>…}` inspect format (`:89`), raw data passed to `call_parallel` (`:337-338`, `:361-364`, and 26 similar calls in the docs), and a Mermaid sample that doesn't match `visualize_mermaid` output (`:403-413`). Status: open (docs-only, low-risk candidate).
- F-25: No `llms.txt` (404 on the docs site). Status: open (add `docs/llms.txt`, as in prompt_manager#24).
- F-26: `docs/development/dagwood-concepts.md` is built and published but not in the mkdocs nav, and it describes `Pipeline#reverse`/`#subgraph`, which don't exist. Status: needs Dewayne's decision (add it to the nav as a proposals page, or move it out of `docs/`).
- F-27: `gem build` warns that `homepage_uri` and `source_code_uri` are the same URL (`simple_flow.gemspec:17-18`). Status: open (fold into the F-11 gemspec metadata PR).
- F-28: `test/thread_fallback_test.rb:51` asserts `duration < 0.025` and fails under load (17 of 20 runs here); `test/optional_steps_test.rb:410` has the same pattern with more slack. Status: merged (low risk) [#23](https://github.com/MadBomber/simple_flow/pull/23), develop 195aa58; the `optional_steps_test.rb:410` case passed 20/20 and was left alone.
- F-29: Parallel steps repeat errors already on the incoming result: each branch carries the inherited errors and the merge concatenates them all (`pipeline.rb:322-327`, `:377-386`). `call_parallel` gives `{input: ["name missing", "name missing"]}`; `call` gives one. Reproduced by the main session. Status: issue [#18](https://github.com/MadBomber/simple_flow/issues/18).
- F-30: Dead code: the `else step_def.call(res)` backward-compatibility branch in `Pipeline#call` (`pipeline.rb:189-200`, 0 coverage; every release stores steps as Hashes) and an unused `require 'delegate'` (`lib/simple_flow.rb:12`). Status: needs Dewayne's decision for the branch, since `steps` is a public reader someone could append to; the `delegate` require is open.
- F-31: `Result#halt(false)` keeps the old value because `result.rb:83` tests truthiness. Status: open (low value).
- F-11 update: the redundant `require_relative 'dependency_graph'` is still at `pipeline.rb:333`.
- F-32: `.rubocop.yml` loads rubocop-minitest and rubocop-performance with `require:`; current RuboCop warns to switch to `plugins:`. Status: open (small chore; touches lint config only).
