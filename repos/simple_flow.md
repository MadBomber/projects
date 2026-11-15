# simple_flow

Workflow/pipeline orchestration gem: immutable `Result`, steps and middleware, parallel execution via `async` fibers with a thread fallback, dependency-graph scheduling and visualization. Runtime dependencies: `logger` and `tsort` only (since #22, on develop). Released 0.4.0 (2026-04-28).

## Links

- GitHub: https://github.com/MadBomber/simple_flow
- RubyGems: https://rubygems.org/gems/simple_flow
- Docs: https://madbomber.github.io/simple_flow/
- Issues: https://github.com/MadBomber/simple_flow/issues

## Status

_Updated 2026-11-15 08:15 (fourteenth visit): #53-#55 still await review, so no new PRs; dependencies audit found nothing new; filed #68, #69._ Suite green on Ruby 4.0.9 and 4.1.0-preview1 (249 runs on develop 2a5f282). develop still has unreleased user-visible fixes (main is 44 commits behind). Dewayne **squash-merges** here. Docs deploy on `docs/**`, `mkdocs.yml` and the workflow on pushes to develop as well as main (README and CHANGELOG don't deploy), so docs PRs are not self-merged. Use `PATH=~/.rbenv/versions/4.0.9/bin:$PATH`; `RBENV_VERSION` alone still picks up 4.1-preview's bundler.

## Backlog

Well-defined items a single small PR could close.

- F-81: unknown `call_parallel(strategy:)` silently becomes `:explicit`. [#54](https://github.com/MadBomber/simple_flow/pull/54) **PR open**
- F-122: `ParallelExecutor.execute_with_async(max_concurrent: 0)` still hangs when called directly (follow-up to #40). [#55](https://github.com/MadBomber/simple_flow/pull/55) **PR open**
- F-84 (F-24): README badges/counts stale, async wording contradicts the thread fallback. [#53](https://github.com/MadBomber/simple_flow/pull/53) **PR open**. Still open: `docs/development/contributing.md:35` pins "77 tests, 296 assertions" (docs deploy).
- [#50](https://github.com/MadBomber/simple_flow/issues/50) `call`/`:explicit` run steps that depend on an unactivated optional step (F-120). Next PR candidate: ~15 lib lines in `pipeline.rb`.
- [#51](https://github.com/MadBomber/simple_flow/issues/51) `call` silently ignores activating an unknown step (F-121). Pairs with #50 (same lines).
- [#52](https://github.com/MadBomber/simple_flow/issues/52) `DependencyGraph#dependencies` shares one mutable default array (F-76). 1 line + test.
- [#46](https://github.com/MadBomber/simple_flow/issues/46) `execution_plan`/visualizers show optional steps as always running (F-98). Needs a display decision (inbox); a fix also removes the note #49 added to `docs/api/dependency-graph-visualizer.md`.
- [#58](https://github.com/MadBomber/simple_flow/issues/58) step-validation error doesn't name the step or what it got (F-82; String names via robot_lab). ~6 lib lines + tests.
- [#57](https://github.com/MadBomber/simple_flow/issues/57) unnamed step silently ignores `depends_on:`, so an "optional" one always runs (F-140). ~5 lib lines in `pipeline.rb`.
- [#56](https://github.com/MadBomber/simple_flow/issues/56) `to_mermaid` breaks on keyword or spaced step names (F-124, verified with mermaid 12.3.0). ~15 lib lines, mirrors #44.
- F-18: `StepTracker`'s doc-comment example (`with_continue`/`.continue` don't exist).
- F-11/F-27: `documentation_uri` to the docs site; gemspec template leftovers (`bindir`/`executables`); `homepage_uri == source_code_uri` build warning.
- F-17: raise the Ruby floor to 3.3 (EOL 3.2; CI can't install on 3.2).
- F-83 remainder: CHANGELOG [Unreleased] misses the Tests CI workflow, RuboCop config, `pipeline_graph.*` untrack, and docs refreshes.
- F-85 + F-101: `-w` unused-variable warnings in examples 05 and 10, and `to_html`'s redundant assignment. F-86: `call_parallel` doc comment lacks `@param max_concurrent`.
- F-32/F-129: `.rubocop.yml` `require:` → `plugins:` (one line; removes two warnings per run). F-25: add `docs/llms.txt`.
- F-38 + F-127: `test.yml` `actions/checkout@v4` → v7 (Node 20 deprecation warning on every job) and its stale io-event matrix comment. `.github/`, so not low risk.
- F-128: `Gemfile.lock` `BUNDLED WITH 2.7.2` crashes on Ruby 4.1; bump to Bundler 4.0.23 together with a `"4.1"` CI entry once 4.1.0 ships (December).
- [#59](https://github.com/MadBomber/simple_flow/issues/59) `call_parallel` runs the first wave on an already-halted input (F-147). 1 lib line in `pipeline.rb` + test.
- [#60](https://github.com/MadBomber/simple_flow/issues/60) `parallel_order` is cubic; visualizers stall on large graphs (F-148). ~5 lib lines in `dependency_graph.rb`, Set-based.
- [#61](https://github.com/MadBomber/simple_flow/issues/61) `use_middleware` accepts any value and fails later with NoMethodError (F-149). ~2 lib lines + test.
- F-150: dependency/activation errors show String names as Symbols (`:#{name}` → `name.inspect`, 3 lines). Pairs with #58.
- F-151: docs site `getting-started/examples.md` lacks example 13 (optional steps). Docs deploy.
- Builder note for #56: quote or escape Mermaid labels so that newlines, `]` and `"` are neutralized too, not just keywords and spaces.
- Builder note: `call_parallel` validates `max_concurrent` only when a parallel group runs, so a bad value is silently accepted or raises after earlier steps ran; validate once at the top of `call_parallel`.
- [#62](https://github.com/MadBomber/simple_flow/issues/62) a parallel step raising `NotImplementedError`/`LoadError` cancels its siblings (F-162). 1 lib line in `parallel_executor.rb` + a test per backend + CHANGELOG.
- [#63](https://github.com/MadBomber/simple_flow/issues/63) `depends_on: [:optional]` not treated as optional (F-163). ~8 lib lines in `pipeline.rb`. Same lines as #57/#64.
- [#64](https://github.com/MadBomber/simple_flow/issues/64) `parallel` accepts `:optional` as a group name (F-156). Two frozen constants used in four places.
- F-161: test-only PR: the halt checks at `pipeline.rb:377` and `:532` are untested (mutation leaves the suite green). Low risk.
- F-158: add `# frozen_string_literal: true` to five lib files (`.rubocop.yml` already enforces it; suite passes with frozen literals on).
- [#65](https://github.com/MadBomber/simple_flow/issues/65) docs landing page: "Get Started" 404s and the Quick Example raises `ArgumentError` (F-166, F-167). Two lines in `docs/index.md` (docs deploy, so not self-merged). Pair with F-165 (favicon) only if mkdocs.yml review is fine.
- [#66](https://github.com/MadBomber/simple_flow/issues/66) documented middleware order, array `activate`, activation across `parallel`, and two halt checks have no tests (F-171 + F-161). Test-only, low risk.
- [#67](https://github.com/MadBomber/simple_flow/issues/67) Pipeline-as-a-step works but is undocumented and untested (F-172). README section + test.
- F-168 + F-126: `testing.md:25` "skips are expected" and `contributing.md:35` pinned counts (docs deploy). F-169 `contributing.md:101` nonexistent test name (rest needs a decision).
- [#68](https://github.com/MadBomber/simple_flow/issues/68) `call_parallel` ignores steps activated on the input Result (F-174). ~5 lib lines at the top of `execute_with_dependency_graph`; same lines as #59, so one PR closes both (after #54 merges).
- [#69](https://github.com/MadBomber/simple_flow/issues/69) `step` silently drops a second callable or a block (F-175). ~4 lib lines + tests; pairs with #58.
- F-152 + F-101: visualizer dead code (`next if group.empty?`, redundant `html =`), 3 lines; a removal to balance recent fix PRs.

## Open bomberbot PRs

- [#53](https://github.com/MadBomber/simple_flow/pull/53) docs(readme): drop pinned test counts and fix async wording, **PR open** (README only; CI green; review cap hit, so not self-merged)
- [#54](https://github.com/MadBomber/simple_flow/pull/54) fix(pipeline): reject an unknown call_parallel strategy, **PR open** (lib + docs notes; CI green; review clean at round 3)
- [#55](https://github.com/MadBomber/simple_flow/pull/55) fix(parallel): validate max_concurrent in execute_with_async too, **PR open** (lib; CI green; review clean in 2 rounds)

## Merged bomberbot PRs

- [#40](https://github.com/MadBomber/simple_flow/pull/40)-[#44](https://github.com/MadBomber/simple_flow/pull/44) squash-merged by Dewayne 2026-11-13 (develop 52e10e1, 404e32b, 506dc52, dce712a, 1f4b734), without comment. Dewayne's own [#48](https://github.com/MadBomber/simple_flow/pull/48) (Closes #45) and [#49](https://github.com/MadBomber/simple_flow/pull/49) (Closes #47) followed the same evening.

- [#39](https://github.com/MadBomber/simple_flow/pull/39) test: silence warnings and stray output in rake test (F-70), **merged (low risk)** by madbot 2026-11-13, develop 77f7527
- [#24](https://github.com/MadBomber/simple_flow/pull/24), [#25](https://github.com/MadBomber/simple_flow/pull/25), [#26](https://github.com/MadBomber/simple_flow/pull/26) (Fixes #18, #20, #19), rebase-merged by Dewayne 2026-11-12 (develop 00d88fe, 21ef8ab/c9be9bd, 2be8921/624fc84)
- [#22](https://github.com/MadBomber/simple_flow/pull/22) fix: declare `logger` and `tsort` as runtime deps (merged 2026-11-11, develop 5132c88, after @copilot resolved a CHANGELOG conflict)
- [#21](https://github.com/MadBomber/simple_flow/pull/21) fix(pipeline): stop `call_parallel` silently skipping steps (merged 2026-11-10, develop 6e8c269, d94d818)
- [#17](https://github.com/MadBomber/simple_flow/pull/17) test: run the VERSION check under rake test (merged 2026-11-10, develop daa8ae7)
- [#23](https://github.com/MadBomber/simple_flow/pull/23) test: prove thread fallback runs steps concurrently (F-28), **merged (low risk)** by bomberbot 2026-11-10, develop 195aa58
- [#16](https://github.com/MadBomber/simple_flow/pull/16) fix(middleware): accept `logger:` keyword in Logging (merged 2026-11-09)
- [#15](https://github.com/MadBomber/simple_flow/pull/15) chore: untrack generated `pipeline_graph.*` files (merged 2026-11-09)
- [#9](https://github.com/MadBomber/simple_flow/pull/9) ci: run minitest suite on push and PR (merged 2026-11-09)
- [#10](https://github.com/MadBomber/simple_flow/pull/10) docs: fix middleware method and async fallback wording (merged 2026-11-09)
- [#11](https://github.com/MadBomber/simple_flow/pull/11) chore(rubocop): make lint runnable, target Ruby 3.2 (merged 2026-11-09)
