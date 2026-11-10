# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## ragdoll

- [ ] Review [ragdoll#19](https://github.com/MadBomber/ragdoll/pull/19) (drops `annotate`; gemspec change, so not merged as low risk). Issue #17 closes only when `develop` reaches `main`; `main` still locks Rails 8.0.3, so 8.1 reaches gem users with the next release. Say if you want the floor raised to `activerecord`/`activejob >= 8.1.4.1`. (added 2026-11-10)
- [ ] Issues #11 and #13 are fixed on `develop` (#15, #14) but stay open until `develop` is merged to `main`, or close them by hand. (added 2026-11-10)

## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23) is merged (Ruby 3.2 floor confirmed). At release: dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer; add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08, updated 2026-11-09)
- [ ] debug_me `main` and `develop` have diverged further. Besides Copilot #21 on `main`, Copilot #29–#35 merged to `main` (closing issues #12, #14, #16, #25, #26, #27 there) while `develop` holds bomberbot's own fixes for the same ground (#8–#24, #36). Two Copilot PRs are still open against `main` (#28, #32). Merge one branch into the other before the next release; the one-PR resolution plan is in the inbox (findings #30). Consider pointing Copilot at `develop` to stop the two tracks diverging. (added 2026-11-08, updated 2026-11-09)

## typed_bus

- [X] Create a `develop` branch in typed_bus (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#1](https://github.com/MadBomber/typed_bus/issues/1)–[#6](https://github.com/MadBomber/typed_bus/issues/6) are ready to become PRs. (added 2026-11-09; #4–#6 added on the second visit)

## trak_flow

- [ ] Create a `develop` branch in trak_flow (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#1](https://github.com/MadBomber/trak_flow/issues/1)–[#3](https://github.com/MadBomber/trak_flow/issues/3) and findings S-2, F-3, C4 are ready to become PRs. Third repo with this gap (after typed_bus, agent99). (added 2026-11-09)

## smart_message

- [ ] Review [smart_message#9](https://github.com/MadBomber/smart_message/pull/9) (drops an unused third-party script from the docs site; details in the bomberbot final report of 2026-11-10). The docs deploy only from `main`, so the live site changes only after `develop` reaches `main` (or cherry-pick #9's `mkdocs.yml` line to `main`) and the Pages deploy runs. The same line is in the `mkdocs.yml` of agent99, htm, ragdoll-docs, sqa, sqa-cli and sqa-tai. (added 2026-11-10)
- [ ] Review [smart_message#11](https://github.com/MadBomber/smart_message/pull/11) (test CI; touches `.github/` and `Gemfile.lock`, so not merged as low risk). After merge, make `test (4.0)` and `test (4.1.0-preview1)` required checks on `develop` (and `main`). (added 2026-11-10)
- [ ] smart_message gemspec floor: `required_ruby_version >= 3.0.0`, but the locked breaker_machines 0.21.2 and chrono_machines 0.11.0 need Ruby 4.0 (CI on #11 failed `bundle install` on 3.3/3.4). Raise the floor to `>= 4.0`, or loosen those two deps if 3.x matters. (added 2026-11-10)
- [ ] Review [smart_message#10](https://github.com/MadBomber/smart_message/pull/10) (DLQ `inspect_messages` fix; `lib/` change). (added 2026-11-10)
