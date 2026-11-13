# simple_flow

Workflow/pipeline orchestration gem: immutable `Result`, steps and middleware, parallel execution via `async` fibers with a thread fallback, dependency-graph scheduling and visualization. Runtime dependencies: `logger` and `tsort` only (since #22, on develop). Released 0.4.0 (2026-04-28).

## Links

- GitHub: https://github.com/MadBomber/simple_flow
- RubyGems: https://rubygems.org/gems/simple_flow
- Docs: https://madbomber.github.io/simple_flow/
- Issues: https://github.com/MadBomber/simple_flow/issues

## Status

_Updated 2026-11-13 03:40 (seventh visit): Dewayne merged #24-#26 and fixed #14, #27-#35 himself on develop; this visit opened #39-#41 (#39 merged as low risk), ran the quality audit and filed #36-#38._ Suite green on Ruby 4.0.9 (225 runs, 0 skips; clean of `-w` warnings after #39). develop still has unreleased user-visible fixes. Docs deploy on `docs/**` pushes to develop as well as main, so docs PRs are not self-merged. Use `PATH=~/.rbenv/versions/4.0.9/bin:$PATH`; `RBENV_VERSION` alone still picks up 4.1-preview's bundler.

## Backlog

Well-defined items a single small PR could close.

- F-72: parallel steps crash on non-`Result` input (regression from #24). [#41](https://github.com/MadBomber/simple_flow/pull/41) **PR open**
- F-74/F-80: `max_concurrent: 0` hangs forever on async. [#40](https://github.com/MadBomber/simple_flow/pull/40) **PR open**
- [#36](https://github.com/MadBomber/simple_flow/issues/36) `visualize_dot` writes step names unquoted (F-63).
- [#37](https://github.com/MadBomber/simple_flow/issues/37) threads backend raises while sibling steps still run (F-73).
- [#38](https://github.com/MadBomber/simple_flow/issues/38) docs show absolute local paths as file locations, plus the `:concurrency:` typo (F-16, F-41).
- F-18: fix `StepTracker`'s doc-comment example (`with_continue`/`.continue` don't exist).
- F-11/F-27: `documentation_uri` to the docs site; gemspec template leftovers (`bindir`/`executables`); `homepage_uri == source_code_uri` build warning.
- F-17: raise the Ruby floor to 3.3 (EOL 3.2; CI can't install on 3.2).
- F-81: unknown `call_parallel(strategy:)` silently becomes `:explicit`. F-82: step-validation error doesn't name the step.
- F-83: CHANGELOG [Unreleased] misses the Tests CI workflow and two docs refreshes. F-84 (F-24): README badges/counts stale, `README.md:524` contradicts the thread fallback.
- F-85: `-w` unused-variable warnings in examples 05 and 10. F-86: `call_parallel` doc comment lacks `@param max_concurrent`.
- F-76: `Hash.new([])` shared default in `DependencyGraph`. F-32: `.rubocop.yml` `require:` → `plugins:`.
- F-25: add `docs/llms.txt`.

## Open bomberbot PRs

- [#40](https://github.com/MadBomber/simple_flow/pull/40) fix(parallel): reject a max_concurrent that would hang, **PR open** (lib; CI green)
- [#41](https://github.com/MadBomber/simple_flow/pull/41) fix(pipeline): accept non-Result input in parallel steps, **PR open** (lib; CI green; fixes our #24 regression)

## Merged bomberbot PRs

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
