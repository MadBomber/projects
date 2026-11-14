# dev

Shared Asgard task libraries (.loki) for madbomber repos. Not a gem: its suite is whatever `test/` holds plus a clean load of every `.loki` file.

## Links

- GitHub: https://github.com/MadBomber/dev
- RubyGems: https://rubygems.org/gems/dev
- Issues: https://github.com/MadBomber/dev/issues

## Status

_Updated 2026-11-14 (ninth run)._ Dewayne merged #22, #25, #26 and fixed every open issue but #29 (his #32 open); this visit opened #38, #39, merged #40 (llms.txt) as low risk at cf827ba, and filed #35–#37.

## Backlog

Well-defined items a single small PR could close. Bomberbot works these before anything else in this repo.

- F-5: drop the header comments copied from the vbr project. `branch_report.loki` part: [dev#38](https://github.com/MadBomber/dev/pull/38) **PR open**; `db_doc.loki` and `.irbrc` still wait on F-6.
- F-39: stale header comments and labels in quality.loki (plus F-46 ArchSpec message, F-49 reek/flay load text). [dev#39](https://github.com/MadBomber/dev/pull/39) **PR open**; the :187 reek_baseline wording and the "Tests + Coverage" label still wait on Dewayne.
- F-54: rename `status_date_check` so `quality` stops collecting it ([dev#35](https://github.com/MadBomber/dev/issues/35)); after #32 merges (same file).
- F-51: notes_collect "today's journal entry" wording; after #32 merges.
- F-56: flog thresholds `>` vs `>=` ([dev#37](https://github.com/MadBomber/dev/issues/37)).

## Open bomberbot PRs

- [dev#38](https://github.com/MadBomber/dev/pull/38): chore(branch_report): drop the copied header line (F-5 part). **PR open**
- [dev#39](https://github.com/MadBomber/dev/pull/39): fix(quality): correct stale comments and skip hints (F-39, F-46, F-49). **PR open**

## Open bomberbot issues

- [dev#29](https://github.com/MadBomber/dev/issues/29) calendar_collect says icalBuddy is missing when it is installed but fails (F-53; Dewayne's #32 open)
- [dev#35](https://github.com/MadBomber/dev/issues/35) quality counts status_date_check as a failing gate (F-54)
- [dev#36](https://github.com/MadBomber/dev/issues/36) build names the gem pkg/<name>-?.?.?.gem when version.rb isn't a literal (F-55)
- [dev#37](https://github.com/MadBomber/dev/issues/37) flog_check thresholds use > though the text says >= (F-56)

Closed by Dewayne's PRs: #7, #8, #9, #13, #14, #15, #23, #24, #27, #28.

## Merged bomberbot PRs

[dev#40](https://github.com/MadBomber/dev/pull/40) llms.txt (adoption, 2026-11-14, **merged (low risk)** at cf827ba), [dev#26](https://github.com/MadBomber/dev/pull/26) `|` in branch_report names (F-13, 2026-11-13), [dev#25](https://github.com/MadBomber/dev/pull/25) one-line abort on a bad date env var (F-45, 2026-11-13), [dev#22](https://github.com/MadBomber/dev/pull/22) branch_report quiet git (F-37, 2026-11-13), [dev#12](https://github.com/MadBomber/dev/pull/12) skip absent flog/flay/reek/bundler-audit (F-27, 2026-11-12), [dev#11](https://github.com/MadBomber/dev/pull/11) reek_baseline task (F-26, 2026-11-13; merged with doubts, see taste), [dev#10](https://github.com/MadBomber/dev/pull/10) STATUS_WEEK_OF snaps to Monday (F-28, 2026-11-12), [dev#6](https://github.com/MadBomber/dev/pull/6) quality gates skip unbundled Rails/fasterer (2026-11-09), [dev#5](https://github.com/MadBomber/dev/pull/5) status_report survives missing base64/claude (F-24, 2026-11-09), [dev#4](https://github.com/MadBomber/dev/pull/4) branch_report vs origin/<default> (F-11, 2026-11-07), [dev#3](https://github.com/MadBomber/dev/pull/3) (2026-11-07), [dev#2](https://github.com/MadBomber/dev/pull/2) (2026-11-07), [dev#1](https://github.com/MadBomber/dev/pull/1) (2026-11-07).
