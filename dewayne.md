# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## asgard

- [ ] Review [asgard#16](https://github.com/MadBomber/asgard/pull/16) (jobs start race: a start right after a job ends could say "already running" and start nothing; this is what turns macOS CI red on `develop`). Lib change, so not merged as low risk. (added 2026-11-10)
- [ ] Review [asgard#12](https://github.com/MadBomber/asgard/pull/12) (`--doctor` reports `exit`/`abort` during `.loki` load instead of dying). Lib change. (added 2026-11-10)
- [ ] Review [asgard#11](https://github.com/MadBomber/asgard/pull/11) (`docs/llms.txt`). Not merged as low risk: merging deploys the docs site (Pages runs on `docs/**` pushes to `develop`) and its review loop hit the cap. After merge, check https://madbomber.github.io/asgard/llms.txt is live. (added 2026-11-10)

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
- [ ] Decide the dev-dependency bloat ([#13](https://github.com/MadBomber/typed_bus/issues/13)/F-31): `aigcm` (AI commit CLI, never used in repo code) and `ratatui_ruby` (native Rust ext, only `examples/15_performance.rb`) dominate the Gemfile, break `bundle exec rake test` on a plain checkout, cause the red YARD-deploy CI, and are the sole source of the dev-tree security advisories (S-32, held local). Options: (a) drop `aigcm` from the committed Gemfile (install it globally instead); (b) move `ratatui_ruby` to an optional/examples-only group; (c) keep both and just unbreak CI via [#13](https://github.com/MadBomber/typed_bus/issues/13). (added 2026-11-10)
- [ ] Enable Dependabot + vulnerability alerts in repo settings (currently off — `dependabot/alerts` returns 403). A PR can add `.github/dependabot.yml` ([#15](https://github.com/MadBomber/typed_bus/issues/15)) but the alerts toggle is admin-only. (added 2026-11-10)

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
- [ ] Review [sqa#52](https://github.com/MadBomber/sqa/pull/52) (drop dead Ruby 1.9 branch + dead `|| const_missing` in `String#to_constant`; Fixes #47). Lib change so not self-merged; rubocop clean, suite 616/0F/0E on 4.0.9, regression test added. (added 2026-11-10)
- [ ] Decide the design for [sqa#4](https://github.com/MadBomber/sqa/issues/4) (API keys in config: env var name vs value vs close); see inbox. (added 2026-11-10)

## robot_lab

- [ ] Merge `main` into `develop`. `develop` (8ec1d6e, VERSION 0.2.6) is 31 commits behind `main` (0.6.0) and 0 ahead, so bomberbot PRs land on stale code; bomberbot may not push `develop` itself. Until then it only builds changes in files that are identical on both branches. (added 2026-11-10)
- [ ] Review [robot_lab#27](https://github.com/MadBomber/robot_lab/pull/27) (untracks `.claude/settings.local.json`). Before pulling the merge into any checkout, back up that file and restore it afterwards, because pulling a commit that deletes a tracked file removes the local copy. It stays ignored after that. (added 2026-11-10)
- [ ] Review [robot_lab#26](https://github.com/MadBomber/robot_lab/pull/26) (removes stray `temp.md` and `logfile`). It wasn't self-merged because `logfile` is neither docs nor tests. (added 2026-11-10)
- [ ] Release the `robot_lab-*` extension gems with a core requirement that admits 0.6.0 ([robot_lab#29](https://github.com/MadBomber/robot_lab/issues/29)). (added 2026-11-10)

## smart_message-transport-named_pipes

- [ ] Review the three new PRs opened on 2026-11-10 visit 2 (all target `develop`): [#9](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/9) (CI running the suite directly, Fixes #7 — live CI green 3.2–3.4; not low-risk as it touches `.github/`), [#10](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/10) (README dependency-claim accuracy, docs-only — eligible for low-risk self-merge but held because its review left a deferred out-of-scope note), [#8](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/8) (remove dead private `publisher_pipe_name`; lib). Five bomberbot PRs now await you here (#4/#5/#8/#9/#10). Whichever of #4/#5/#8 you merge first, the other two need a trivial CHANGELOG `[Unreleased]` rebase. (added 2026-11-10)
- [ ] Decide F-2: remove the public `attr_reader :publisher_pipes` (always `{}`, never populated — the publisher `.out.pipe` side was never wired up)? It's public API, so removal needs a deprecation cycle. The dead *private* method is already gone via #8. (added 2026-11-10)
- [ ] Decide F-3: the documented config options `:mode` and `:buffer_size` are inert (never read in lib). Implement them or remove them from `DEFAULT_CONFIG`/YARD/README? Both are documented public options. (added 2026-11-10)
- [ ] Decide F-10: message framing uses `pipe.puts`/`pipe.gets` + `strip`, so a serialized payload containing a newline is truncated at the first `\n`. Single-line JSON is fine today; a robust fix (length-prefix or escaping) is a wire-format change. Implement or document the single-line constraint? (added 2026-11-10)

- [ ] Review [#5](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/5) (single-source the `VERSION` constant via `version.rb`; lib change, so not auto-merged). Base `develop`, clean review, suite 45/0F. (added 2026-11-10)
- [ ] Review [#4](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/4) (stop packaging the test-only `transport/base.rb` stub; gemspec change). The stub currently ships in the built gem and can shadow the real `smart_message` base on an installed consumer's load path — see `notes/security/smart_message-transport-named_pipes.md` (S-5) for the framing kept out of the public PR. (added 2026-11-10)
- [ ] Decide the `smart_message` dependency floor for [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6): lower to `>= 0.0.17` or publish `smart_message` 0.1.0 first. The gem is uninstallable until this is resolved (and CI in #7 can't use `bundle` until then). (added 2026-11-10)
- [ ] Decide F-14 (README teaches a transport API the real smart_message rejects), F-18 (#6 also needs smart_message's own runtime deps fixed), F-19 (Ruby floor 3.3 vs 4.0). Options in `inbox.md` (visit 3). (added 2026-11-10)
- [ ] Security: first audit found local-privilege/correctness issues (S-1..S-6) held locally in `notes/security/smart_message-transport-named_pipes.md` — notably a mkfifo→chmod TOCTOU on a predictable world-readable `/tmp` path and the signal-handler hijack. None remotely exploitable; your call on hardening/disclosure. (added 2026-11-10)

## simple_flow

- [ ] Review [simple_flow#22](https://github.com/MadBomber/simple_flow/pull/22) (declares `logger` and `tsort` as runtime deps; gemspec, so not merged as low risk). Released 0.4.0 raises `LoadError` on `require "simple_flow"` in any Ruby 4.0 Bundler app that doesn't list `logger` itself, so users only get the fix with a release from `develop`. If you'd rather keep "zero runtime dependencies", the PR body describes the alternative. (added 2026-11-10)
- [ ] Review [simple_flow#21](https://github.com/MadBomber/simple_flow/pull/21) (Fixes #12, #13; lib change). It changes behaviour on purpose: a `depends_on:` that names an unknown step, or a parallel group declared later, now raises `ArgumentError` instead of silently skipping the step. (added 2026-11-10)

## smart_message-transport-lanet

- [ ] Review [#6](https://github.com/MadBomber/smart_message-transport-lanet/pull/6) (test CI on Ruby 3.3 and 3.4, green). After merging, you can optionally make `test (3.3)` and `test (3.4)` required checks on `develop` and `main`. Also decide the Ruby floor: the gemspec says `>= 3.2.0`, but the locked smart_message 0.0.17 pulls in breaker_machines 0.7.0 and chrono_machines 0.2.0, which both need 3.3. Raise the floor to 3.3, or relock so 3.2 can go back into the matrix. (added 2026-11-10)
- [ ] Review [#5](https://github.com/MadBomber/smart_message-transport-lanet/pull/5) (tests load on Ruby 4 and minitest 6; test-only). When the Gemfile moves past `minitest ~> 5`, also add `gem "minitest-mock"`. (added 2026-11-10)
- [ ] Decide F-1: the transport calls a Lanet API that doesn't exist. Options are in inbox.md (2026-11-10). Issues #2–#4 and most of the backlog depend on this. (added 2026-11-10)
- [ ] Security: the first audit found S-1…S-6, held locally in `notes/security/smart_message-transport-lanet.md`. All are latent while F-1 stands, and the gem is unpublished, but any F-1 rewrite must cover S-1…S-4. S-6 is advisories in the dev lockfile, fixed by a relock. (added 2026-11-10)
- [ ] Review [#7](https://github.com/MadBomber/smart_message-transport-lanet/pull/7) (README examples match the smart_message 0.0.17 API; README only). Also decide F-19: should a node receive its own published messages? `publish_to_nodes` never delivers locally. (added 2026-11-10)
