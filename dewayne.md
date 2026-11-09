# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23) is merged (Ruby 3.2 floor confirmed). At release: dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer; add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08, updated 2026-11-09)
- [ ] debug_me `main` and `develop` have diverged further. Besides Copilot #21 on `main`, Copilot #29–#35 merged to `main` (closing issues #12, #14, #16, #25, #26, #27 there) while `develop` holds bomberbot's own fixes for the same ground (#8–#24, #36). Two Copilot PRs are still open against `main` (#28, #32). Merge one branch into the other before the next release; the one-PR resolution plan is in the inbox (findings #30). Consider pointing Copilot at `develop` to stop the two tracks diverging. (added 2026-11-08, updated 2026-11-09)

## typed_bus

- [ ] Create a `develop` branch in typed_bus (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#1](https://github.com/MadBomber/typed_bus/issues/1)–[#6](https://github.com/MadBomber/typed_bus/issues/6) are ready to become PRs. (added 2026-11-09; #4–#6 added on the second visit)

## agent99

- [ ] Create a `develop` branch in agent99 (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#3](https://github.com/MadBomber/agent99/issues/3)–[#5](https://github.com/MadBomber/agent99/issues/5) and findings F-1/F-2/F-6..F-9 are ready to become PRs. (added 2026-11-09, first visit)
