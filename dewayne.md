# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## aia

- [ ] Review [aia#172](https://github.com/MadBomber/aia/pull/172) (removes two dead `mkdocs.yml` asset lines + the decommissioned polyfill.io CDN, Fixes #169). Not merged as low risk because `mkdocs.yml` is the config the docs site deploys from. The docs deploy runs on push to both `main` and `develop`, so the live site changes once this reaches those branches. The open question of whether to drop the whole MathJax/arithmatex stack (no math content found in `docs/`) is in `inbox.md`. (added 2026-11-10)

## ragdoll

- [ ] Review [ragdoll#19](https://github.com/MadBomber/ragdoll/pull/19) (drops `annotate`; gemspec change, so not merged as low risk). Issue #17 closes only when `develop` reaches `main`; `main` still locks Rails 8.0.3, so 8.1 reaches gem users with the next release. Say if you want the floor raised to `activerecord`/`activejob >= 8.1.4.1`. (added 2026-11-10)
- [ ] Issues #11 and #13 are fixed on `develop` (#15, #14) but stay open until `develop` is merged to `main`, or close them by hand. (added 2026-11-10)

## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23) is merged (Ruby 3.2 floor confirmed). At release: dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer; add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08, updated 2026-11-09)
- [ ] debug_me `main` and `develop` have diverged further. Besides Copilot #21 on `main`, Copilot #29–#35 merged to `main` (closing issues #12, #14, #16, #25, #26, #27 there) while `develop` holds bomberbot's own fixes for the same ground (#8–#24, #36). Two Copilot PRs are still open against `main` (#28, #32). Merge one branch into the other before the next release; the one-PR resolution plan is in the inbox (findings #30). Consider pointing Copilot at `develop` to stop the two tracks diverging. (added 2026-11-08, updated 2026-11-09)

## typed_bus

- [ ] Review and merge into `develop` (not `main`) the three bomberbot PRs, each resolving an open issue: [#9](https://github.com/MadBomber/typed_bus/pull/9) (throttle hang at full capacity, Fixes #2), [#8](https://github.com/MadBomber/typed_bus/pull/8) (DLQ drain keeps entries on raise, Fixes #6), [#7](https://github.com/MadBomber/typed_bus/pull/7) (declare `logger` runtime dep, Fixes #1). None were auto-merged as low risk because all touch `lib/` or the gemspec. The `Fixes #N` issues close when `develop` reaches `main` at the next release. (added 2026-11-10)

## agent99

- [ ] Review and merge into `develop` [#12](https://github.com/MadBomber/agent99/pull/12) (Fixes #8, lib) and [#13](https://github.com/MadBomber/agent99/pull/13) (Fixes #6, lib), plus the still-open [#10](https://github.com/MadBomber/agent99/pull/10) and [#11](https://github.com/MadBomber/agent99/pull/11) (gemspec). Each adds a CHANGELOG line, so the next tender rebases whatever merges after the first. (added 2026-11-10)
- [ ] Decide for [#17](https://github.com/MadBomber/agent99/pull/17): do the header helpers (`to_uuid`, `from_uuid`, `event_uuid`, `timestamp`, `type`, `return_address`) stay public? #17 pins them public as they behave today; `header_management.rb` has a commented-out `# private`. Pairs with F-19. (added 2026-11-10)
- [ ] Issues #3/#4/#5… won't auto-close: `Fixes #N` only fires on merges into `main`. Close #4 by hand now (fixed on develop via #9) or at release. (added 2026-11-10)
- [ ] The agent99 docs site is 404 (GitHub Pages disabled, F-25). Before re-enabling Pages, handle the held-local note S-6 for this repo (same one-line `mkdocs.yml` fix as smart_message#9), then redeploy. (added 2026-11-10)

## trak_flow

- [ ] Create a `develop` branch in trak_flow (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#1](https://github.com/MadBomber/trak_flow/issues/1)–[#3](https://github.com/MadBomber/trak_flow/issues/3) and findings S-2, F-3, C4 are ready to become PRs. Third repo with this gap (after typed_bus, agent99). (added 2026-11-09)

## smart_message

- [ ] Review [smart_message#9](https://github.com/MadBomber/smart_message/pull/9) (drops an unused third-party script from the docs site; details in the bomberbot final report of 2026-11-10). The docs deploy only from `main`, so the live site changes only after `develop` reaches `main` (or cherry-pick #9's `mkdocs.yml` line to `main`) and the Pages deploy runs. The same line is in the `mkdocs.yml` of agent99, htm, ragdoll-docs, sqa, sqa-cli and sqa-tai. (added 2026-11-10)
- [ ] Review [smart_message#11](https://github.com/MadBomber/smart_message/pull/11) (test CI; touches `.github/` and `Gemfile.lock`, so not merged as low risk). After merge, make `test (4.0)` and `test (4.1.0-preview1)` required checks on `develop` (and `main`). (added 2026-11-10)
- [ ] smart_message gemspec floor: `required_ruby_version >= 3.0.0`, but the locked breaker_machines 0.21.2 and chrono_machines 0.11.0 need Ruby 4.0 (CI on #11 failed `bundle install` on 3.3/3.4). Raise the floor to `>= 4.0`, or loosen those two deps if 3.x matters. (added 2026-11-10)
- [ ] Review [smart_message#10](https://github.com/MadBomber/smart_message/pull/10) (DLQ `inspect_messages` fix; `lib/` change). (added 2026-11-10)

## gem-skill

- [ ] Review and merge into `develop` [gem-skill#10](https://github.com/MadBomber/gem-skill/pull/10) (bundle flag parsing, F-3/F-4; lib change, so not low risk). Note the behaviour change: `bundle skill` now exits 1 on an unknown, abbreviated or value-less flag, where before it silently ignored it. (added 2026-11-10)
- [ ] Review [gem-skill#9](https://github.com/MadBomber/gem-skill/pull/9) (tests only, F-12). Not merged as low risk because its review loop hit the 3-round cap. Optional: `~/sandbox/git_repos/madbomber/gem-skill/.claude/skills/my_gem` in your checkout is the dangling link this leak left on 2026-09-24; it's safe to `rm`. (added 2026-11-10)

## scripts

- [ ] Review [scripts#25](https://github.com/MadBomber/scripts/pull/25), which re-lands the README index fixes from #22 (they merged into the stale stack branch `bomber/readme-fix-rows`, not develop). After it merges, close [#19](https://github.com/MadBomber/scripts/issues/19) by hand (`Fixes` doesn't auto-close for PRs into develop), then delete the branches `bomber/readme-fix-rows` and `bomber/readme-index-complete`. (added 2026-11-10)
- [ ] Review [scripts#26](https://github.com/MadBomber/scripts/pull/26) (FileUtils copy, Fixes #23) and [scripts#24](https://github.com/MadBomber/scripts/pull/24) (sinatra template APP_BIND error). Both are code changes, so neither qualified for a low-risk merge. (added 2026-11-10)
- [ ] scripts: security findings S-2..S-4 are held locally in `notes/security/scripts.md`. S-2 has a small fix that bomberbot will open next visit under a neutral title unless you say otherwise. (added 2026-11-10)

## sqa

- [ ] Review [sqa#44](https://github.com/MadBomber/sqa/pull/44) (remove two stray root files). Not merged as low risk because it addresses S-1; see `notes/security/sqa.md` for the decisions only you can make (history and already-published gems). (added 2026-11-10)
- [ ] Review [sqa#46](https://github.com/MadBomber/sqa/pull/46) (README examples match the API). Docs only, but its review round left 3 out-of-scope findings open, so it wasn't self-merged. (added 2026-11-10)
- [ ] Decide the design for [sqa#4](https://github.com/MadBomber/sqa/issues/4) (API keys in config: env var name vs value vs close); see inbox. (added 2026-11-10)

## robot_lab

- [ ] Merge `main` into `develop`. `develop` (8ec1d6e, VERSION 0.2.6) is 31 commits behind `main` (0.6.0) and 0 ahead, so bomberbot PRs land on stale code; bomberbot may not push `develop` itself. Until then it only builds changes in files that are identical on both branches. (added 2026-11-10)
