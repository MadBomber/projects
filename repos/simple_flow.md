# simple_flow

Workflow/pipeline orchestration gem: immutable `Result`, steps and middleware, parallel execution via `async` fibers with a thread fallback, dependency-graph scheduling and visualization. Zero runtime dependencies. Released 0.4.0 (2026-04-28).

## Links

- GitHub: https://github.com/MadBomber/simple_flow
- RubyGems: https://rubygems.org/gems/simple_flow
- Docs: https://madbomber.github.io/simple_flow/
- Issues: https://github.com/MadBomber/simple_flow/issues

## Status

_Updated 2026-11-09 (second visit): #9–#11 from the first visit merged; opened three small PRs (Logging `logger:` crash, tracked generated graph files, a test file that never runs) and filed #12–#14 for `call_parallel` silently skipping steps and stale example baselines._ Suite green on Ruby 4.0.9 (193 runs); Tests CI runs on 3.3 and 3.4. `bundle exec` fails under Ruby 4.1.0-preview1 (bundler 2.7.2), as elsewhere.

## Backlog

Well-defined items a single small PR could close.

- [#12](https://github.com/MadBomber/simple_flow/issues/12) `call_parallel` must raise on an unknown `depends_on:` (F-1).
- [#13](https://github.com/MadBomber/simple_flow/issues/13) named steps in an unnamed `parallel do` block never run under `call_parallel` (F-3).
- [#14](https://github.com/MadBomber/simple_flow/issues/14) regenerate stale example regression baselines (F-10).
- F-16: API docs show an absolute local path as each class's **Location**.
- F-17: raise the Ruby floor to 3.3 (EOL 3.2; CI already can't install on 3.2).
- F-11: `documentation_uri` to the docs site; gemspec template leftovers.
- F-18: fix `StepTracker`'s doc-comment example.

## Open bomberbot PRs

- [#16](https://github.com/MadBomber/simple_flow/pull/16) fix(middleware): accept `logger:` keyword in Logging (F-9), **PR open**

## Merged bomberbot PRs

- [#9](https://github.com/MadBomber/simple_flow/pull/9) ci: run minitest suite on push and PR (merged 2026-11-09)
- [#10](https://github.com/MadBomber/simple_flow/pull/10) docs: fix middleware method and async fallback wording (merged 2026-11-09)
- [#11](https://github.com/MadBomber/simple_flow/pull/11) chore(rubocop): make lint runnable, target Ruby 3.2 (merged 2026-11-09)
