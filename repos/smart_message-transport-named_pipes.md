# smart_message-transport-named_pipes

A Ruby gem (v0.0.1) providing a Unix named pipes (FIFO) transport for the SmartMessage
pub/sub messaging system — fast inter-process communication between processes on the same
machine. Unix-only (needs `mkfifo`). Runtime-depends on `smart_message`. `lib/` is three
files: the transport, a committed test stub `transport/base.rb`, and a `version.rb`.

## Links

- GitHub: https://github.com/MadBomber/smart_message-transport-named_pipes
- RubyGems: https://rubygems.org/gems/smart_message-transport-named_pipes
- Issues: https://github.com/MadBomber/smart_message-transport-named_pipes/issues

## Status

_Updated 2026-11-10 (first visit): created `develop` from `main` at 8d0d2af (the repo had only
`main`). Suite is green run directly (`ruby -Itest -Ilib`): 45 runs, 0 failures, 9 skips — but
`bundle install` fails because the gemspec requires `smart_message >= 0.1.0` while RubyGems only
publishes 0.0.17 (uninstallable, [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6)).
First security + dead-code audit ran (S-1…S-6, F-1…F-4). Opened three PRs against `develop`:
[#3](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/3) README accuracy
(docs, **merged low-risk**), [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5)
single-source VERSION (lib), [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4)
stop packaging the test stub (gemspec) — #4/#5 await Dewayne. Filed [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6)
(uninstallable dep floor) and [#7](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/7) (no CI)._

## Backlog

Well-defined items a single small PR could close.

- **F-5 / [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6):** gemspec `smart_message >= 0.1.0` is unsatisfiable (RubyGems max 0.0.17) → gem
  uninstallable. Needs Dewayne's call on the floor (lower to 0.0.17 vs publish 0.1.0). In inbox.
- **F-4:** single-source the duplicated `VERSION`. **PR open [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5).**
- **S-5:** stop shipping the test stub `transport/base.rb` in the gem. **PR open [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4).**
- **F-6 / [#7](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/7):** add CI that runs the suite directly (no bundle; no rubocop yet — ~137 pre-existing
  offenses).
- **F-2:** remove vestigial `publisher_pipes` — but `attr_reader :publisher_pipes` is public API →
  needs Dewayne's decision (inbox).
- **F-3:** `mode` / `buffer_size` options are documented but inert → implement or remove (inbox).
- **F-7:** README "zero dependencies" wording vs the `smart_message` runtime dep.

## Open bomberbot PRs

- [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5) `madbot/version-single-source` — single-source the `VERSION` constant via `version.rb` (removes the inline duplicate; lib-only). Base `develop`, rebased onto `develop` after #3 merged, MERGEABLE, clean review, suite 45/0F. Awaiting Dewayne (lib change). Source: finding F-4.
- [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4) `madbot/exclude-stub-from-gem` — stop packaging the test-only `transport/base.rb` stub in the built gem (gemspec `spec.files`). Base `develop`, rebased, MERGEABLE, clean review, suite 45/0F. Awaiting Dewayne (gemspec change). Source: audit S-5 (shipped stub shadows the real `smart_message` base; PR body is neutral).

## Merged bomberbot PRs

- [#3](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/3) `madbot/readme-accuracy` — corrected the README channel-derivation example and stale `Running Tests` file paths. **Merged into develop as low risk by madbot** (docs-only, clean review) at `2a49fcc`. Source: improver.
