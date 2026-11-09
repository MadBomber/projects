# aia

AI Assistant CLI gem; depends on prompt_manager and shared_tools.

## Links

- GitHub: https://github.com/MadBomber/aia
- RubyGems: https://rubygems.org/gems/aia
- Issues: https://github.com/MadBomber/aia/issues

## Status

_Updated 2026-11-09 (03:45 visit)._ Review queue full: #166/#167/#168 all still open and unapproved, so no new PRs this visit (skipped per the 3-open rule). Ran the first adoption audit (develop d974374, unchanged since the 02:00 visit): findings F-21..F-26, all net-new. Filed two issues — #169 (dead docs-site assets + decommissioned polyfill.io CDN, F-21/F-22) and #170 (README `/ruby` omits the `--allow-ruby-eval` gate, F-23). No issue activity and no approvals since 02:00, so the tender had nothing to merge/rebase and the responder had nothing non-duplicate to add.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- (none of the old items open; F-10 merged in #160. Small-PR targets for next visit once the review queue clears: issues [#169](https://github.com/MadBomber/aia/issues/169) (remove dead mkdocs assets + polyfill.io, F-21/F-22) and [#170](https://github.com/MadBomber/aia/issues/170) (one-line README `/ruby` gate note, F-23); #165 (test-harness exit, F-14); F-5 redundant per-file requires is a low-value removal.)

## Open bomberbot PRs

- [#166](https://github.com/MadBomber/aia/pull/166) docs: correct README configuration tables to match defaults.yml (resolves #163/F-18) (**PR open**)
- [#167](https://github.com/MadBomber/aia/pull/167) fix(skills): recruit and reskill tools accept only skill-directory ids (security S-2) (**PR open**)
- [#168](https://github.com/MadBomber/aia/pull/168) test: replace deprecated SimpleCov calls (resolves #164/F-17) (**PR open**)

## Merged bomberbot PRs

- [#160](https://github.com/MadBomber/aia/pull/160) fix: DelegateHandler and Foreman tool require their classes (merged 2026-11-07)
- [#159](https://github.com/MadBomber/aia/pull/159) fix(test): failing test runs exit non-zero (merged 2026-11-07)
- [#158](https://github.com/MadBomber/aia/pull/158) fix(test): every unit test file runs on its own (merged 2026-11-07)
- [#157](https://github.com/MadBomber/aia/pull/157) fix(test): run unit suite without API keys or UTF-8 locale (merged 2026-11-07)
