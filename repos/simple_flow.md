# simple_flow

Workflow/pipeline orchestration gem: immutable `Result`, steps and middleware, parallel execution via `async` fibers with a thread fallback, dependency-graph scheduling and visualization. Runtime dependencies: `logger` and `tsort` only (since #22, on develop). Released 0.4.0 (2026-04-28).

## Links

- GitHub: https://github.com/MadBomber/simple_flow
- RubyGems: https://rubygems.org/gems/simple_flow
- Docs: https://madbomber.github.io/simple_flow/
- Issues: https://github.com/MadBomber/simple_flow/issues

## Status

_Updated 2026-11-12 04:40 (fourth visit): Dewayne merged #17, #21 and #22 (rebase merges); opened [#24](https://github.com/MadBomber/simple_flow/pull/24) (Fixes #18), [#25](https://github.com/MadBomber/simple_flow/pull/25) (Fixes #20) and [#26](https://github.com/MadBomber/simple_flow/pull/26) (Fixes #19); filed #27-#29; first dependencies audit._ Suite green on Ruby 4.0.9 (203 runs). develop has unreleased user-visible fixes (Ruby 4 load, `call_parallel` skips, Logging `logger:`). Docs deploy on `docs/**` pushes to develop as well as main, so docs PRs are not self-merged. Use `PATH=~/.rbenv/versions/4.0.9/bin:$PATH`; `RBENV_VERSION` alone still picks up 4.1-preview's bundler.

## Backlog

Well-defined items a single small PR could close.

- [#14](https://github.com/MadBomber/simple_flow/issues/14) regenerate stale example regression baselines (F-10).
- F-16: API docs show an absolute local path as each class's **Location**.
- F-17: raise the Ruby floor to 3.3 (EOL 3.2; CI already can't install on 3.2).
- F-11: `documentation_uri` to the docs site; gemspec template leftovers.
- F-18: fix `StepTracker`'s doc-comment example.
- [#18](https://github.com/MadBomber/simple_flow/issues/18) parallel steps repeat errors already on the incoming result (F-29). [#24](https://github.com/MadBomber/simple_flow/pull/24) **PR open**
- [#19](https://github.com/MadBomber/simple_flow/issues/19) docs site says the async fallback is sequential and documents a nonexistent `execute_sequential` (F-20, F-21). [#26](https://github.com/MadBomber/simple_flow/pull/26) **PR open**
- [#20](https://github.com/MadBomber/simple_flow/issues/20) stale installation and testing docs pages (F-22). installation.md already fixed on develop; testing.md in [#25](https://github.com/MadBomber/simple_flow/pull/25) **PR open**
- [#27](https://github.com/MadBomber/simple_flow/issues/27) add Ruby 4.0 to the Tests CI matrix (F-33).
- [#28](https://github.com/MadBomber/simple_flow/issues/28) dev-dependency refresh with simplecov `skip` (F-34, F-36).
- [#29](https://github.com/MadBomber/simple_flow/issues/29) `benchmarks/parallel_vs_sequential.rb` crashes on Ruby 4.0 (F-39).
- Removal: unused `require 'delegate'` in `lib/simple_flow.rb` and redundant `require_relative 'dependency_graph'` in `pipeline.rb` (F-30/F-11 halves, verified green).
- F-25: add `docs/llms.txt`. F-24: README nits (hard-coded test counts, old inspect format).

## Open bomberbot PRs

- [#24](https://github.com/MadBomber/simple_flow/pull/24) fix: report inherited errors once after parallel steps (Fixes #18), **PR open**
- [#25](https://github.com/MadBomber/simple_flow/pull/25) docs: drop stale test counts and describe CI accurately (Fixes #20), **PR open**
- [#26](https://github.com/MadBomber/simple_flow/pull/26) docs: describe the Ruby thread fallback, not a sequential one (Fixes #19), **PR open**

## Merged bomberbot PRs

- [#22](https://github.com/MadBomber/simple_flow/pull/22) fix: declare `logger` and `tsort` as runtime deps (merged 2026-11-11, develop 5132c88, after @copilot resolved a CHANGELOG conflict)
- [#21](https://github.com/MadBomber/simple_flow/pull/21) fix(pipeline): stop `call_parallel` silently skipping steps (merged 2026-11-10, develop 6e8c269, d94d818)
- [#17](https://github.com/MadBomber/simple_flow/pull/17) test: run the VERSION check under rake test (merged 2026-11-10, develop daa8ae7)
- [#23](https://github.com/MadBomber/simple_flow/pull/23) test: prove thread fallback runs steps concurrently (F-28), **merged (low risk)** by bomberbot 2026-11-10, develop 195aa58
- [#16](https://github.com/MadBomber/simple_flow/pull/16) fix(middleware): accept `logger:` keyword in Logging (merged 2026-11-09)
- [#15](https://github.com/MadBomber/simple_flow/pull/15) chore: untrack generated `pipeline_graph.*` files (merged 2026-11-09)
- [#9](https://github.com/MadBomber/simple_flow/pull/9) ci: run minitest suite on push and PR (merged 2026-11-09)
- [#10](https://github.com/MadBomber/simple_flow/pull/10) docs: fix middleware method and async fallback wording (merged 2026-11-09)
- [#11](https://github.com/MadBomber/simple_flow/pull/11) chore(rubocop): make lint runnable, target Ruby 3.2 (merged 2026-11-09)
