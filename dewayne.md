# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## asgard

- [ ] Review [asgard#16](https://github.com/MadBomber/asgard/pull/16) (jobs start race: a start right after a job ends could say "already running" and start nothing; this is what turns macOS CI red on `develop`). Lib change, so not merged as low risk. (added 2026-11-10)
- [ ] Review [asgard#12](https://github.com/MadBomber/asgard/pull/12) (`--doctor` reports `exit`/`abort` during `.loki` load instead of dying). Lib change. (added 2026-11-10)
- [ ] Review [asgard#11](https://github.com/MadBomber/asgard/pull/11) (`docs/llms.txt`). Not merged as low risk: merging deploys the docs site (Pages runs on `docs/**` pushes to `develop`) and its review loop hit the cap. After merge, check https://madbomber.github.io/asgard/llms.txt is live. (added 2026-11-10)

## aia


## ragdoll

- [ ] Review [ragdoll#19](https://github.com/MadBomber/ragdoll/pull/19) (drops `annotate`; gemspec change, so not merged as low risk). Issue #17 closes only when `develop` reaches `main`; `main` still locks Rails 8.0.3, so 8.1 reaches gem users with the next release. Say if you want the floor raised to `activerecord`/`activejob >= 8.1.4.1`. (added 2026-11-10)
- [ ] Issues #11 and #13 are fixed on `develop` (#15, #14) but stay open until `develop` is merged to `main`, or close them by hand. (added 2026-11-10)

## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23) is merged (Ruby 3.2 floor confirmed). At release: dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer; add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08, updated 2026-11-09)
- [ ] debug_me `main` and `develop` have diverged further. Besides Copilot #21 on `main`, Copilot #29–#35 merged to `main` (closing issues #12, #14, #16, #25, #26, #27 there) while `develop` holds bomberbot's own fixes for the same ground (#8–#24, #36). Two Copilot PRs are still open against `main` (#28, #32). Merge one branch into the other before the next release; the one-PR resolution plan is in the inbox (findings #30). Consider pointing Copilot at `develop` to stop the two tracks diverging. (added 2026-11-08, updated 2026-11-09)

## typed_bus

- [ ] Review and merge into `develop` the three bomberbot PRs from 2026-11-12 (none self-merged: two touch `lib/`, one rewrites a released CHANGELOG heading). All CI green; they merge cleanly together: [#30](https://github.com/MadBomber/typed_bus/pull/30) (a publish blocked on backpressure or throttle no longer delivers after `close`, F-15), [#29](https://github.com/MadBomber/typed_bus/pull/29) (`drain` with `break` no longer re-queues the entry it just handled, F-49, follow-up to #23), [#28](https://github.com/MadBomber/typed_bus/pull/28) (CHANGELOG: drain bullet matches #23, LICENSE note, `[0.1.0]` heading → `[0.0.1] - 2026-02-07` with the rename and throttle bullets moved under it). (added 2026-11-12)
- [ ] At the next release, close the issues fixed on `develop` (#1-#6, #10-#21) if `Fixes #N` doesn't do it; those keywords only auto-close on merge to `main`. BomberBot left a "fixed on develop in <sha>" note on each. (added 2026-11-11, updated 2026-11-12)
- [ ] Enable Dependabot vulnerability alerts in repo settings (still off: `dependabot/alerts` returns 403). `.github/dependabot.yml` now exists (4f26122), but the alerts toggle is admin-only. (added 2026-11-10)

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
- [ ] Review and merge [gem-skill#11](https://github.com/MadBomber/gem-skill/pull/11) (verify truncation fix, F-25; lib change, not low risk). Fixes #6. One-line guard so a truncated `--verify` reply no longer overwrites the cached skill. (added 2026-11-10)
- [ ] Review and merge [gem-skill#12](https://github.com/MadBomber/gem-skill/pull/12) (exit codes, F-26; lib change, not low risk). Fixes #7. Behaviour change: `gem skill` now exits 1 on error paths (unknown subcommand, missing name, failed per-gem install/verify) instead of 0; error (1) takes precedence over verify-fixed (2). (added 2026-11-10)
- [ ] Review and merge [gem-skill#13](https://github.com/MadBomber/gem-skill/pull/13) (S-1 path validation, neutral title; lib change, not low risk). Adds a `Gem::Skill::Validation` module and applies it at the lockfile/cache/linker boundaries. Security context is in the bomberbot final report of 2026-11-10, not in the PR. (added 2026-11-10)

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

- [ ] Merge `main` into `develop`. `develop` (now 0304617, VERSION 0.2.6) is 31 commits behind `main` (0.6.0) and a few ahead (bomberbot merges), so bomberbot PRs land on stale code; bomberbot may not push `develop` itself. Until then it only builds changes in files that are identical on both branches. Extra reason to do this soon: develop's stale gemspec still pins `ruby_llm "~> 1.12"`, which resolves to a version carrying 3 published High ReDoS CVEs (main 0.6.0 is already on ruby_llm 2.1.1 and audits clean), so the sync also clears that. (added 2026-11-10, CVE note 2026-11-10 v2)
- [ ] Review [robot_lab#27](https://github.com/MadBomber/robot_lab/pull/27) (untracks `.claude/settings.local.json`). Before pulling the merge into any checkout, back up that file and restore it afterwards, because pulling a commit that deletes a tracked file removes the local copy. It stays ignored after that. (added 2026-11-10)
- [ ] Review [robot_lab#26](https://github.com/MadBomber/robot_lab/pull/26) (removes stray `temp.md` and `logfile`). It wasn't self-merged because `logfile` is neither docs nor tests. (added 2026-11-10)
- [ ] Release the `robot_lab-*` extension gems with a core requirement that admits 0.6.0 ([robot_lab#29](https://github.com/MadBomber/robot_lab/issues/29)). (added 2026-11-10)

## smart_message-transport-named_pipes

- [ ] Review [#20](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/20) (Fixes #13: `unsubscribe` dispatcher key; lib). Then review [#21](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/21) (Fixes #15: `unsubscribe!` leaks pipe+reader; stacked on #20, retarget to `develop` after #20 merges). Close #13 by hand after merge (Fixes only auto-closes on `main`). (added 2026-11-12)
- [ ] Close [#11](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/11) (fixed on develop by low-risk [#16](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/16)) and [#7](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/7) (fixed on develop by #9) when you see fit. (added 2026-11-12)
- [ ] Decide F-24/F-14: README Complete Example and both examples use `attribute :x, :type`, which no smart_message provides; rewrite them to the verified framework path (`from`/`property`/`config { transport }`/`Msg.subscribe`)? Also F-26 (gem never released, docs say it is) and F-30 (throughput claim 10–100× off). Options in `inbox.md`. (added 2026-11-12)

- [ ] Decide F-2: remove the public `attr_reader :publisher_pipes` (always `{}`, never populated — the publisher `.out.pipe` side was never wired up)? It's public API, so removal needs a deprecation cycle. The dead *private* method is already gone via #8. (added 2026-11-10)
- [ ] Decide F-3: the documented config options `:mode` and `:buffer_size` are inert (never read in lib). Implement them or remove them from `DEFAULT_CONFIG`/YARD/README? Both are documented public options. (added 2026-11-10)
- [ ] Decide F-10: message framing uses `pipe.puts`/`pipe.gets` + `strip`, so a serialized payload containing a newline is truncated at the first `\n`. Single-line JSON is fine today; a robust fix (length-prefix or escaping) is a wire-format change. Implement or document the single-line constraint? (added 2026-11-10)

- [ ] Decide the `smart_message` dependency floor for [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6): lower to `>= 0.0.17` or publish `smart_message` 0.1.0 first. The gem is uninstallable until this is resolved (and CI in #7 can't use `bundle` until then). (added 2026-11-10)
- [ ] Decide F-14 (README teaches a transport API the real smart_message rejects), F-18 (#6 also needs smart_message's own runtime deps fixed), F-19 (Ruby floor 3.3 vs 4.0). Options in `inbox.md` (visit 3). (added 2026-11-10)
- [ ] Security: first audit found local-privilege/correctness issues (S-1..S-6) held locally in `notes/security/smart_message-transport-named_pipes.md` — notably a mkfifo→chmod TOCTOU on a predictable world-readable `/tmp` path and the signal-handler hijack. None remotely exploitable; your call on hardening/disclosure. (added 2026-11-10)

## simple_flow

- [ ] Review [simple_flow#22](https://github.com/MadBomber/simple_flow/pull/22) (declares `logger` and `tsort` as runtime deps; gemspec, so not merged as low risk). Released 0.4.0 raises `LoadError` on `require "simple_flow"` in any Ruby 4.0 Bundler app that doesn't list `logger` itself, so users only get the fix with a release from `develop`. If you'd rather keep "zero runtime dependencies", the PR body describes the alternative. (added 2026-11-10)
- [ ] Review [simple_flow#21](https://github.com/MadBomber/simple_flow/pull/21) (Fixes #12, #13; lib change). It changes behaviour on purpose: a `depends_on:` that names an unknown step, or a parallel group declared later, now raises `ArgumentError` instead of silently skipping the step. (added 2026-11-10)

## smart_message-transport-lanet

- [ ] Decide F-1: the transport calls a Lanet API that doesn't exist. Options are in inbox.md (2026-11-10). Issue #3 and most of the backlog depend on this. (added 2026-11-10)
- [ ] Security: the first audit found S-1…S-7, held locally in `notes/security/smart_message-transport-lanet.md`. All are latent while F-1 stands, and the gem is unpublished, but any F-1 rewrite must cover S-1…S-4. S-6 is cleared by [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13). (added 2026-11-10, updated 2026-11-12)
- [ ] Decide F-19: should a node receive its own published messages? `publish_to_nodes` never delivers locally. (added 2026-11-10)
- [ ] Review [#8](https://github.com/MadBomber/smart_message-transport-lanet/pull/8) (Fixes #4, reconnect after disconnect; 1 lib line + tests) and [#9](https://github.com/MadBomber/smart_message-transport-lanet/pull/9) (Fixes #2, Class-keyed subscribe; `network_topology[:active_subscriptions]` now returns Strings). Both touch `lib/`. Both add a CHANGELOG line, so whichever merges second needs a trivial rebase; BomberBot will do it next visit. (added 2026-11-12)
- [ ] Review [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13) (relock so the dev bundle installs on Ruby 4; CI runs 3.3, 3.4 and 4.0). Optional after merging: (a) make the hold durable with `gem "breaker_machines", "~> 0.17.0", force_ruby_platform: true` in the Gemfile, or else a `bundle update` on Ruby 4 or a relock on Linux will break the 3.3 job; (b) make `test (4.0)` a required check. (added 2026-11-12)
- [ ] Decide the Ruby floor (F-24): the gemspec says `>= 3.2.0` and `.rubocop.yml` targets 3.2, but no lock installs on 3.2, and smart_message's dependency chain (breaker_machines ≥ 0.18, chrono_machines ≥ 0.9.1) now needs Ruby ≥ 4.0. Options: floor 3.3 and keep holding those gems back (#13 does this), or floor 4.0 and drop 3.x from CI. (added 2026-11-10, updated 2026-11-12)
- [ ] Optional: turn on "Automatically delete head branches" for this repo (merged bot branches pile up; the tender deletes them by hand). (added 2026-11-12)

## htm (2026-11-11)

- [ ] Review [htm#32](https://github.com/MadBomber/htm/pull/32) (Fixes #19; adds `NodeTag.link`, which restores a soft-deleted link or creates one, fixes `NodeTag#restore!`, and swaps the six `find_or_create` call sites). Touches `lib/`, so not self-merged. 652 tests green. (added 2026-11-11)
- [ ] Review [htm#31](https://github.com/MadBomber/htm/pull/31) (makes `HTM_PROPOSITION__ENABLED=false` actually disable extraction; config coercion plus test). Touches `lib/htm/config.rb`, so not self-merged. 648 tests green. (added 2026-11-11)
- [ ] Review [htm#30](https://github.com/MadBomber/htm/pull/30) (`htm_mcp help` text: `HTM_CHUNKING__CHUNK_SIZE/CHUNK_OVERLAP`, the proposition default, the `fiber` backend and the connection timeout; plus a test). Touches `lib/htm/mcp/cli.rb`, so not self-merged. 650 tests green. (added 2026-11-11)
- [ ] Optional: turn on "Automatically delete head branches" for htm. Merged bot branches pile up otherwise; this visit deleted ten. (added 2026-11-11)

