# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-09._ #160 merged and v2.1.0 released (#162). This visit opened three PRs: #166 (README config tables, resolves #163/F-18), #167 (recruit/reskill tools confine skill ids, security S-2), #168 (replace deprecated SimpleCov calls, resolves #164/F-17). develop green at d974374 (1995 runs, 0 failures).

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none open; F-10 merged in #160. #165 is an open issue a small test-harness PR could resolve next visit; F-5 redundant per-file requires is a low-value removal.)

## Open bomberbot PRs

- [#166](https://github.com/MadBomber/aia/pull/166) docs: correct README configuration tables to match defaults.yml (resolves #163/F-18) (**PR open**)
- [#167](https://github.com/MadBomber/aia/pull/167) fix(skills): recruit and reskill tools accept only skill-directory ids (security S-2) (**PR open**)
- [#168](https://github.com/MadBomber/aia/pull/168) test: replace deprecated SimpleCov calls (resolves #164/F-17) (**PR open**)

## Merged bomberbot PRs

- [#160](https://github.com/MadBomber/aia/pull/160) fix: DelegateHandler and Foreman tool require their classes (merged 2026-11-07)
- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (merged 2026-11-07)
- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (merged 2026-11-07)
- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
