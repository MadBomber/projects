# Findings: simple_flow

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision, issue #N.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-12 | Second run, at develop 5132c88 (audited a git-archive of origin/develop). Lib still has no shell-outs, deserialization, ENV reads or file IO; `instance_eval` only for the DSL. bundle-audit: dev-only hits (S-3). Found F-66, F-67; S-4..S-6 held locally (S-4/S-5 widen S-1-era notes). Earlier: 2026-11-09 First run (first visit, which was cut off before it wrote brain), at develop 915656e. The released gem has zero runtime dependencies, no shell-outs, no deserialization, no regexps in lib. S-1, S-2 held locally. |
| dead code | 2026-11-12 | Full scan (lib, examples, benchmarks, bin, test; dependents fact_db, htm, robot_lab, robot_lab-cyborg, robot_lab-ractor) at develop 5132c88, Ruby 4.0.9, debride 1.15.2 + RuboCop unused cops + SimpleCov (497/502). Every debride lib candidate is public API or a TSort callback. New: F-42..F-44 (one verified removal, issue #32), F-45/F-46 need a decision; stale remote branches noted. Earlier: 2026-11-09 lib-only brief pass at e8196cc. |
| dependencies | 2026-11-12 | First run, at develop 5132c88, Ruby 4.0.9 (4.1.0-preview1 without Bundler: 203/203 pass). Runtime deps logger/tsort correct, guarded by gemspec_test. CI lacks 4.0 (F-33, issue #27). Full dev refresh green (F-34, F-36, issue #28). aigcm unused (F-35). Dependabot off (F-37). Actions majors behind (F-38). io-event pin can't restore 3.2 (F-17 update). S-3 held locally. Earlier note: | See F-19 (logger/tsort runtime deps) from the 2026-11-10 adoption audit. Triage noted: Dependabot alerts disabled; dev-group minor bumps available (async 2.46, rubocop 1.92); minitest 6 and simplecov 1.x are majors. |
| adoption | 2026-11-10 | First run, at develop f58e221, Ruby 4.0.9. 13/13 examples exit 0; README snippets run with stubs; `mkdocs build --strict` passes (INFO: dagwood-concepts.md not in nav; Material for MkDocs EOL May 2027); CHANGELOG complete for v0.1.0-v0.4.0; no llms.txt. Found F-19..F-27; F-19 (gem won't load on Ruby 4.0 under Bundler) also covers the Ruby 4 part of a dependencies audit. |
| quality | 2026-11-09 | First run, at develop e8196cc, Ruby 4.0.9. Suite 193 runs, 0 failures, 2 skips. Found F-1..F-7, mostly `call_parallel` silently skipping steps. |

Dead-code scan last covered commit: 5132c88 (develop, 2026-11-12)

## Findings

### 2026-11-09 (first visit, cut off before recording; security audit, develop 915656e)

- S-1: security finding, details held locally. Status: needs Dewayne's decision.
- S-2: security finding, details held locally. Status: open (informational).

### 2026-11-09 (second visit; quality audit + brief dead-code pass, develop e8196cc)

- F-1: `call_parallel` (`strategy: :auto`) silently skips a step whose `depends_on:` names an unknown step; `call` runs it. `pipeline.rb:111` stores deps unvalidated; the graph loop ends with no error. Re-reproduced by the main session. Fix: raise `ArgumentError` at the start of `execute_with_dependency_graph`. Status: merged [#21](https://github.com/MadBomber/simple_flow/pull/21) (develop d94d818); issue #12 stays open until develop reaches main.
- F-2: `call_parallel` (`:auto`) drops every unnamed step once any named step exists (`pipeline.rb:209`, `:332-403`). Status: needs Dewayne's decision (raise on mixed pipelines vs fall back to explicit parallelism).
- F-3: Named steps inside an unnamed `parallel do` block never run under `call_parallel` (`pipeline.rb:151-163` writes `@step_dependencies` only for named groups). Re-reproduced by the main session. Status: merged [#21](https://github.com/MadBomber/simple_flow/pull/21) (develop 6e8c269); issue #13 stays open until develop reaches main.
- F-4: `depends_on:` a parallel group declared later is not expanded (`expand_dependencies` runs at declaration time, `pipeline.rb:277-287`), so the dependent step never runs. Fix: expand at run time. Status: open (medium-sized; after #12).
- F-5: Duplicate step names overwrite in `@named_steps` (so `call_parallel` runs only the second) but both run in `call` (`pipeline.rb:103`, `:114`, `:487`). Fix: raise on a duplicate name. Status: open (behaviour change; issue first).
- F-6: `ParallelBlock#step` ignores `depends_on:` and skips the reserved-name check (`pipeline.rb:480-488`). Status: open for the reserved-name check; the `depends_on:` keyword needs Dewayne's decision.
- F-7: `test/test_simple_flow.rb` never runs under `rake test` (Rakefile pattern `test/**/*_test.rb`); it holds the only VERSION assertion. Status: merged [#17](https://github.com/MadBomber/simple_flow/pull/17) (develop daa8ae7).
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

- F-19: On Ruby 4.0, `require "simple_flow"` raises `LoadError` in any Bundler app that doesn't list `logger` itself (`lib/simple_flow.rb:13`; the gemspec declares no runtime deps; `logger` left the default gems in 4.0.0). `tsort` (`dependency_graph.rb:3`) follows in 4.1.0. Hidden by the dev-group `logger` in the Gemfile and the 3.3/3.4-only CI. Reproduced with a consumer app on 4.0.9. Status: merged [#22](https://github.com/MadBomber/simple_flow/pull/22) (develop 5132c88).
- F-20: Docs-site pages say the no-async fallback is sequential (`docs/concurrent/performance.md:7,34-42`, `docs/api/parallel-step.md:9,81`, `docs/api/pipeline.md:269`, `docs/getting-started/examples.md:400`); the code uses threads. Status: issue [#19](https://github.com/MadBomber/simple_flow/issues/19).
- F-21: `docs/api/parallel-step.md:55-68` documents `ParallelExecutor.execute_sequential`, which was replaced by `execute_with_threads` in 13820c4 before v0.1.0 and is in no release. The improver's history check settles it as a docs error, not a missing method. Status: issue [#19](https://github.com/MadBomber/simple_flow/issues/19) (folded in with F-20).
- F-22: `docs/getting-started/installation.md` (Ruby `>= 2.7.0`, `async` "automatically installed") and `docs/development/testing.md` (121 tests, CI on 2.7-3.3 for every push) are stale. Status: issue #20; installation.md half already fixed on develop; testing.md half PR'd [#25](https://github.com/MadBomber/simple_flow/pull/25).
- F-23: `docs/api/pipeline.md` omits `concurrency:`, named `parallel :name, depends_on:` groups, and the `concurrency`/`parallel_groups` readers; typo `:concurrency: :async` at :171. Named groups appear nowhere in the README, docs or examples. Status: open (document after #13 lands).
- F-24: README nits: hard-coded test counts (`README.md:5`, `:484-485`), the old `{:validation=>…}` inspect format (`:89`), raw data passed to `call_parallel` (`:337-338`, `:361-364`, and 26 similar calls in the docs), and a Mermaid sample that doesn't match `visualize_mermaid` output (`:403-413`). Status: open (docs-only, low-risk candidate).
- F-25: No `llms.txt` (404 on the docs site). Status: open (add `docs/llms.txt`, as in prompt_manager#24).
- F-26: `docs/development/dagwood-concepts.md` is built and published but not in the mkdocs nav, and it describes `Pipeline#reverse`/`#subgraph`, which don't exist. Status: needs Dewayne's decision (add it to the nav as a proposals page, or move it out of `docs/`).
- F-27: `gem build` warns that `homepage_uri` and `source_code_uri` are the same URL (`simple_flow.gemspec:17-18`). Status: open (fold into the F-11 gemspec metadata PR).
- F-28: `test/thread_fallback_test.rb:51` asserts `duration < 0.025` and fails under load (17 of 20 runs here); `test/optional_steps_test.rb:410` has the same pattern with more slack. Status: merged (low risk) [#23](https://github.com/MadBomber/simple_flow/pull/23), develop 195aa58; the `optional_steps_test.rb:410` case passed 20/20 and was left alone.
- F-29: Parallel steps repeat errors already on the incoming result: each branch carries the inherited errors and the merge concatenates them all (`pipeline.rb:322-327`, `:377-386`). `call_parallel` gives `{input: ["name missing", "name missing"]}`; `call` gives one. Reproduced by the main session. Status: issue #18; PR'd [#24](https://github.com/MadBomber/simple_flow/pull/24).
- F-30: Dead code: the `else step_def.call(res)` backward-compatibility branch in `Pipeline#call` (`pipeline.rb:189-200`, 0 coverage; every release stores steps as Hashes) and an unused `require 'delegate'` (`lib/simple_flow.rb:12`). Status: needs Dewayne's decision for the branch, since `steps` is a public reader someone could append to; the `delegate` require is open.
- F-31: `Result#halt(false)` keeps the old value because `result.rb:83` tests truthiness. Status: open (low value).
- F-11 update: the redundant `require_relative 'dependency_graph'` is still at `pipeline.rb:333`.
- F-32: `.rubocop.yml` loads rubocop-minitest and rubocop-performance with `require:`; current RuboCop warns to switch to `plugins:`. Status: open (small chore; touches lint config only).

### 2026-11-12 (fourth visit; dependencies audit, develop 5132c88)

- F-33: The Tests CI matrix has no Ruby 4.0 (`.github/workflows/test.yml:20` is `["3.3", "3.4"]`); the locked bundle installs and passes on 4.0.9 (203 runs, 0 failures). Status: issue #27.
- S-3: security finding, details held locally. Status: open.
- F-34: Dev-group gems are behind (minitest 5→6, simplecov 0.22→1.3, rubocop-minitest/-performance pinned by `~>` patch constraints, rubocop 1.86→1.92, async 2.39→2.46). A full refresh is green on 4.0.9 and keeps Ruby 3.3. Not low risk (lockfile churn). Status: issue #28.
- F-35: `aigcm` in the Gemfile dev group is used by nothing in the repo and pulls ~12 gems. Status: needs Dewayne's decision (drop, or keep as his commit-message tool).
- F-36: simplecov 1.x deprecates `SimpleCov.add_filter` (`test/test_helper.rb:6-8`); switch to `skip` with the F-34 bump. Status: issue #28 (folded in).
- F-37: Dependabot is off: no `.github/dependabot.yml` and vulnerability alerts disabled. Status: needs Dewayne's decision (repo setting; possible PR noise).
- F-38: Workflows pin `actions/checkout@v4` (latest v7) and `actions/setup-python@v5` (deploy workflow). Status: open (bump `test.yml` alone; leave the deploy workflow to Dewayne).
- F-17 update: the `test.yml` comment's alternative (pin `io-event < 1.15`) wouldn't restore 3.2: locked async 2.39 and rubocop's `parallel` also need >= 3.3. Strengthens the floor bump. Status: still open, Dewayne's call.
- F-39 (improver): `benchmarks/parallel_vs_sequential.rb:82` crashes on Ruby 4.0 under Bundler: `undefined method 'realtime' for module Benchmark` (benchmark-ips doesn't load stdlib `benchmark`, a bundled gem on 4.0). Status: issue #29. 2026-11-12: `require "benchmark"` fails under Bundler (not in the lockfile); the working fix is `Process.clock_gettime` deltas, posted on #29.
- F-40 (improver): `pipeline.rb:381-382` (error concat in the `call_parallel` dependency-graph path) had 0 coverage. Status: PR'd [#24](https://github.com/MadBomber/simple_flow/pull/24) (its new tests cover the rewritten merge).
- F-41 (improver): `docs/api/pipeline.md:171` typo `:concurrency: :async`; named parallel groups still undocumented (F-23). Status: open.
- Dead-code note: F-30's unused `require 'delegate'` (`lib/simple_flow.rb:12`) and the F-11 redundant `require_relative 'dependency_graph'` (now `pipeline.rb:329`) are still present at 5132c88; the improver verified deleting both keeps the suite green. Status: issue #32 (with F-42..F-44).

### 2026-11-12 07:24 (fifth visit; full dead-code audit, develop 5132c88; improver F-60..F-64)

- F-42: `build_active_dependencies` takes an `executed_steps` argument it never uses (`pipeline.rb:417`, only caller `:338`; RuboCop `Lint/UnusedMethodArgument`). Status: issue #32.
- F-43: `find_next_executable_group` assigns `ready_steps` only to return it (`pipeline.rb:446-453`, `Style/RedundantAssignment`). Status: issue #32.
- F-44: `benchmarks/pipeline_overhead.rb:13-14` defines `simple_op` and never uses it. Status: issue #32.
- Removal bundle (F-30 `delegate` half, F-11 require half, F-42..F-44) verified in a scratch clone: 3 files, +3/-10, suite green alone (203 runs) and merged with #24 (209 runs), RuboCop 175 → 172 offenses, none new. Touches lib, not low risk.
- F-45: `Pipeline#apply_middleware` is public though documented "Internal"; `ParallelBlock#step` calls it via `send` (`pipeline.rb:496,503`) and writes `@named_steps` via `instance_variable_get` (`:498`) despite a public `named_steps` reader. No caller in tests, examples, README or the five dependents. Status: needs Dewayne's decision (make private, or keep public and drop the `send`).
- F-46: public readers with no caller anywhere: `Pipeline#middlewares`, `Pipeline#named_steps`, `DependencyGraphVisualizer#graph`. Public API, so not dead. Status: needs Dewayne's decision; recommend keep (as F-12).
- F-30 update: the `else step_def.call(res)` branch (`pipeline.rb:192-193`) still has 0 coverage; once gone, `call` and `execute_with_explicit_parallelism` are the same reduce.
- F-12 update: `subgraph`/`merge`/`reverse_order` are still test-only; none of the five dependents call them. F-6 update: `ParallelBlock#step`'s `depends_on:` is still accepted and ignored (`pipeline.rb:491`).
- Housekeeping: merged bot branches still on the remote (`bomberbot/declare-logger-tsort`, `bomberbot/call-parallel-skips`, `bomber/run-version-test`, five 0-ahead `bomber/*`); `delete_branch_on_merge` is off. Old `claude/*` branches (one 8 ahead) and `optional_step` are Dewayne's call. Open PRs #24-#26 use `madbot/` branches (allowed prefix).
- F-60 (improver): `ParallelExecutor.execute_with_async` (`parallel_executor.rb:46`) uses top-level `Async do`, which neither waits nor re-raises; a raising parallel step surfaces as `NoMethodError: undefined method 'value' for nil` instead of its own error (reproduced by the main session). One-word fix `Sync do`, trial green (203 runs) and still parallel. Status: issue #30.
- F-61 (improver): same root cause; parallel execution inside an existing reactor (Falcon, async-job, `Sync {}`) crashes with the same `NoMethodError` once steps do I/O. Status: issue #30.
- F-62 (improver): `call_parallel` silently skips steps in a `depends_on` cycle or a self-dependency (reproduced: context `{c: 1}`, `:a`/`:b` never ran); `validate_dependencies!` only checks unknown names. Fix: rescue `TSort::Cyclic` into `ArgumentError`, as #21 did for unknown deps. Status: issue #31.
- F-63 (improver): `to_dot` interpolates node IDs unquoted, so names like `:"fetch-user"` or `:save?` produce DOT that Graphviz rejects. Small `dot_id` quoting helper keeps ordinary output byte-identical. Status: open.
- F-64 (improver): an empty `parallel {}` block raises `NoMethodError` at `pipeline.rb:325`. Status: needs Dewayne's decision (raise at declaration, or no-op).
- F-65 (improver): failing parallel steps are noisy on stderr (async "unhandled exception" warning; threads' `report_on_exception`). Status: inbox (changes visible output).
- Triage: F-16 absolute paths confirmed in 7 docs files besides `testing.md` (#25 fixes that one); F-18 `StepTracker` doc example still calls `with_continue`/`continue`, which don't exist. Both still open, good candidates once the queue drains.

### 2026-11-12 07:45 (sixth visit; security audit, develop 5132c88; improver F-68..F-71)

- F-66 (auditor, improver): `DependencyGraphVisualizer#to_html` raises `NoMethodError` unless `json` is already loaded (nothing in lib requires it; async loads it as a side effect). Verified. Fix with or after S-5. Status: issue #33.
- F-67 (auditor): `call_parallel(max_concurrent:)` is silently ignored on the threads backend, including `:auto` without async (6 steps, cap 2: threads peak 6, async peak 2). Status: needs Dewayne's decision (implement a thread cap, or warn/raise).
- F-68 (improver): a `parallel` block drops incoming `activated_steps` under `call`/`:explicit` (`pipeline.rb:325`); verified `[:x]` vs `[]`. Build after #24 (same lines). Status: issue #34.
- F-69 (improver): the two "async unavailable" tests in `concurrency_parameter_test.rb` always skip, so `pipeline.rb:292` is uncovered. Status: issue #35.
- F-70 (improver): `rake test` prints two `-w` warnings (`middleware_test.rb:27` indentation, `parallel_execution_test.rb:37` unused `result`) and Instrumentation output from `simple_flow_test.rb`. Test-only fix, ~8 lines. Status: open.
- F-71 (improver): `MiddleWare::Instrumentation` times with `Time.now`, not a monotonic clock. Status: open (touches the F-14 file).
- S-4: security finding, details held locally. Status: needs Dewayne's decision.
- S-5: security finding, details held locally. Status: open.
- S-6: security finding, details held locally. Status: needs Dewayne's decision.
