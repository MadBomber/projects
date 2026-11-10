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

_Updated 2026-11-10 (visit 2): `develop` unmoved since visit 1 (2a49fcc); Dewayne hasn't reviewed
#4/#5 yet. Queue was 2 (under the 3-cap), so new work ran. Quality audit ran (F-8…F-13); the
richest find is a verified runtime bug — the INT/TERM signal handler crashes with `ThreadError:
can't be called from trap context` (mutex in trap context), so graceful shutdown is broken
([#12](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/12)). Opened three
PRs against `develop`:
[#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9) CI (Fixes #7, green
3.2–3.4), [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10) README
dependency-claim accuracy (docs, F-7), [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8)
remove dead `publisher_pipe_name` (F-1). **No self-merges** — #10 qualified as low-risk but its
review left a deferred out-of-scope note (sqa#46 taste rule → not self-merged). Filed issues
[#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11) (example nested
defs) and [#12](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/12) (signal
bug). Five bomberbot PRs now await Dewayne (#4/#5/#8/#9/#10). Suite green direct (45→44 on #8);
`bundle install` still broken by the dep floor (#6)._

**Visit 1 (2026-11-10):** created `develop` from `main` at 8d0d2af (repo had only `main`). First
security + dead-code audit (S-1…S-6, F-1…F-4). Opened #3 (README accuracy, **merged low-risk** at
2a49fcc), #5 (VERSION single-source), #4 (stop packaging the test stub). Filed #6 (uninstallable
dep floor) and #7 (no CI).

## Backlog

Well-defined items a single small PR could close.

- **F-5 / [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6):** gemspec `smart_message >= 0.1.0` is unsatisfiable (RubyGems max 0.0.17) → gem
  uninstallable. Needs Dewayne's call on the floor (lower to 0.0.17 vs publish 0.1.0). In inbox.
- **F-4:** single-source the duplicated `VERSION`. **PR open [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5).**
- **S-5:** stop shipping the test stub `transport/base.rb` in the gem. **PR open [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4).**
- **F-6 / [#7](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/7):** add CI that runs the suite directly. **PR open [#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9)** (Fixes #7, green 3.2–3.4).
- **F-7:** README "zero dependencies" wording vs the `smart_message` runtime dep. **PR open [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10).**
- **F-1:** remove dead private `publisher_pipe_name`. **PR open [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8).**
- **F-11 / [#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11):** hoist nested `def`s in `examples/*.rb` (ready PR next visit).
- **F-8 / [#12](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/12):** signal handler crashes (mutex in trap context) → graceful shutdown broken. Fix needs an opt-in/self-pipe design.
- **F-2:** remove vestigial `publisher_pipes` **public** `attr_reader` — public API →
  needs Dewayne's decision (inbox). (The private method is gone via #8.)
- **F-3:** `mode` / `buffer_size` options are documented but inert → implement or remove (inbox).
- **F-10:** newline framing corrupts multi-line payloads → wire-format decision (inbox).

## Open bomberbot PRs

- [#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9) `madbot/ci-tests-workflow` — add `.github/workflows/ci.yml` running the suite directly (`ruby -Itest -Ilib`, no bundler/rubocop) on Ruby 3.2–3.4. Base `develop`, MERGEABLE, **live CI green** on all three, clean review (round 1). Fixes #7. Not low-risk (`.github/`) → awaiting Dewayne. Source: issue #7 / finding F-6. _Note: pushing `.github/workflows/*` needs an SSH remote or a token with `workflow` scope._
- [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10) `madbot/readme-deps-accuracy` — correct the false "Zero External Dependencies" README claim + comparison-table cell to reflect the `smart_message` runtime dep (README-only, 4 lines, no CHANGELOG). Base `develop`, MERGEABLE, suite 45/0F. Docs-only/low-risk-eligible but **not self-merged**: review left a deferred out-of-scope note (sqa#46 rule). Awaiting Dewayne. Source: finding F-7.
- [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8) `madbot/remove-dead-publisher-pipe` — remove the dead private `publisher_pipe_name` + its dedicated test; keep the public `attr_reader :publisher_pipes` (F-2, Dewayne's call). Base `develop`, MERGEABLE, suite 44/0F (one test dropped), clean review. Not low-risk (lib) → awaiting Dewayne. Source: finding F-1.
- [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5) `madbot/version-single-source` — single-source the `VERSION` constant via `version.rb` (lib-only). Base `develop`, MERGEABLE, clean review, suite 45/0F. Awaiting Dewayne. Source: finding F-4. _(Visit 1; CHANGELOG `[Unreleased]` line conflicts with #4/#8 once one merges — expect a rebase.)_
- [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4) `madbot/exclude-stub-from-gem` — stop packaging the test-only `transport/base.rb` stub in the built gem (gemspec `spec.files`). Base `develop`, MERGEABLE, clean review, suite 45/0F. Awaiting Dewayne (gemspec change). Source: audit S-5 (PR body neutral). _(Visit 1.)_

## Merged bomberbot PRs

- [#3](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/3) `madbot/readme-accuracy` — corrected the README channel-derivation example and stale `Running Tests` file paths. **Merged into develop as low risk by madbot** (docs-only, clean review) at `2a49fcc`. Source: improver.
