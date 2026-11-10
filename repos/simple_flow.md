# simple_flow

Workflow/pipeline orchestration gem: immutable `Result`, steps and middleware, parallel execution via `async` fibers with a thread fallback, dependency-graph scheduling and visualization. Zero runtime dependencies. Released 0.4.0 (2026-04-28).

## Links

- GitHub: https://github.com/MadBomber/simple_flow
- RubyGems: https://rubygems.org/gems/simple_flow
- Docs: https://madbomber.github.io/simple_flow/
- Issues: https://github.com/MadBomber/simple_flow/issues

## Status

_Updated 2026-11-10 04:45 (third visit): #15 and #16 merged; opened [#21](https://github.com/MadBomber/simple_flow/pull/21) (`call_parallel` silent skips, Fixes #12/#13) and [#22](https://github.com/MadBomber/simple_flow/pull/22) (the gem fails to load on Ruby 4.0 under Bundler: declare `logger`/`tsort`); merged [#23](https://github.com/MadBomber/simple_flow/pull/23) (flaky timing test) as low risk; filed #18–#20._ Suite green on Ruby 4.0.9 (196 runs) now that the timing flake is gone; Tests CI runs on 3.3 and 3.4 only, so nothing on CI catches a Ruby 4 regression. `bundle exec` fails under Ruby 4.1.0-preview1 (bundler 2.7.2), as elsewhere.

## Backlog

Well-defined items a single small PR could close.

- [#12](https://github.com/MadBomber/simple_flow/issues/12) `call_parallel` must raise on an unknown `depends_on:` (F-1). [#21](https://github.com/MadBomber/simple_flow/pull/21) **PR open**
- [#13](https://github.com/MadBomber/simple_flow/issues/13) named steps in an unnamed `parallel do` block never run under `call_parallel` (F-3). [#21](https://github.com/MadBomber/simple_flow/pull/21) **PR open**
- [#14](https://github.com/MadBomber/simple_flow/issues/14) regenerate stale example regression baselines (F-10).
- F-16: API docs show an absolute local path as each class's **Location**.
- F-17: raise the Ruby floor to 3.3 (EOL 3.2; CI already can't install on 3.2).
- F-11: `documentation_uri` to the docs site; gemspec template leftovers.
- F-18: fix `StepTracker`'s doc-comment example.
- F-19: Ruby 4.0 `LoadError` under Bundler (`logger`, then `tsort` in 4.1). [#22](https://github.com/MadBomber/simple_flow/pull/22) **PR open**
- [#18](https://github.com/MadBomber/simple_flow/issues/18) parallel steps repeat errors already on the incoming result (F-29).
- [#19](https://github.com/MadBomber/simple_flow/issues/19) docs site says the async fallback is sequential and documents a nonexistent `execute_sequential` (F-20, F-21).
- [#20](https://github.com/MadBomber/simple_flow/issues/20) stale installation and testing docs pages (F-22).
- F-25: add `docs/llms.txt`. F-24: README nits (hard-coded test counts, old inspect format).

## Open bomberbot PRs

- [#21](https://github.com/MadBomber/simple_flow/pull/21) fix(pipeline): stop `call_parallel` silently skipping steps (Fixes #12, #13), **PR open**
- [#22](https://github.com/MadBomber/simple_flow/pull/22) fix: declare `logger` and `tsort` as runtime deps (F-19, Ruby 4), **PR open**
- [#17](https://github.com/MadBomber/simple_flow/pull/17) test: run the VERSION check under rake test (F-7), **PR open** (rebased 2026-11-10)

## Merged bomberbot PRs

- [#23](https://github.com/MadBomber/simple_flow/pull/23) test: prove thread fallback runs steps concurrently (F-28), **merged (low risk)** by bomberbot 2026-11-10, develop 195aa58
- [#16](https://github.com/MadBomber/simple_flow/pull/16) fix(middleware): accept `logger:` keyword in Logging (merged 2026-11-09)
- [#15](https://github.com/MadBomber/simple_flow/pull/15) chore: untrack generated `pipeline_graph.*` files (merged 2026-11-09)
- [#9](https://github.com/MadBomber/simple_flow/pull/9) ci: run minitest suite on push and PR (merged 2026-11-09)
- [#10](https://github.com/MadBomber/simple_flow/pull/10) docs: fix middleware method and async fallback wording (merged 2026-11-09)
- [#11](https://github.com/MadBomber/simple_flow/pull/11) chore(rubocop): make lint runnable, target Ruby 3.2 (merged 2026-11-09)
