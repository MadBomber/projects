# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## asgard

- [ ] Review [asgard#16](https://github.com/MadBomber/asgard/pull/16) (jobs start race: a start right after a job ends could say "already running" and start nothing; this is what turns macOS CI red on `develop`). Lib change, so not merged as low risk. (added 2026-11-10)
- [ ] Review [asgard#12](https://github.com/MadBomber/asgard/pull/12) (`--doctor` reports `exit`/`abort` during `.loki` load instead of dying). Lib change. (added 2026-11-10)
- [ ] Review [asgard#11](https://github.com/MadBomber/asgard/pull/11) (`docs/llms.txt`). Not merged as low risk: merging deploys the docs site (Pages runs on `docs/**` pushes to `develop`) and its review loop hit the cap. After merge, check https://madbomber.github.io/asgard/llms.txt is live. (added 2026-11-10)

## aia

- [ ] Let bots push GitHub Actions workflows: `gh auth refresh -h github.com -s workflow` (the token lacks `workflow` scope), or add aia's test CI yourself from `bomberbot/plans/aia-test-ci.md`. aia has no test CI today. (added 2026-11-13)
- [ ] Decide whether aia should require `ruby_llm >= 2.2` (see local security notes for aia, S-5, after #219). (added 2026-11-13)
- [ ] Decide how aia's `develop` gets main's 6 post-release commits, including the `--paint` fix (F-45/F-46; options in inbox.md). (added 2026-11-12)

## ragdoll

- [ ] Before merging [ragdoll#30](https://github.com/MadBomber/ragdoll/pull/30): confirm the `MIT-LICENSE` copyright line, `Copyright (c) 2025 Dewayne VanHoozer` (gemspec author; 2025 is the repo's first commit; wording matches aia and prompt_manager). (added 2026-11-12)
- [ ] Issues #11, #13, #17, #21, #22 and #23 are fixed on `develop` (#15, #14, #19, #25, #26, #24) but stay open until `develop` is merged to `main`, or close them by hand. `develop` holds about eight user-facing fixes that no release has (last tag v0.1.12). (added 2026-11-10, updated 2026-11-12)

## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23) is merged (Ruby 3.2 floor confirmed). At release: dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer; add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08, updated 2026-11-09)
- [ ] At the next debug_me release, close [debug_me#39](https://github.com/MadBomber/debug_me/issues/39) (fixed on `develop` by #43). The main/develop split is settled: `develop` now contains all of `main` (checked 2026-11-12). (added 2026-11-12)
- [ ] Review [debug_me#48](https://github.com/MadBomber/debug_me/pull/48) (touches lib/, so it was not self-merged). Also decide debug_me S-36 and S-37; the details are in your local `notes/security/debug_me.md`. (added 2026-11-12)
- [ ] Review [debug_me#52](https://github.com/MadBomber/debug_me/pull/52) (fixes #44 and #45; touches lib/). Visible change for ordinary classes: when an enclosing module and the receiver's superclass both define a constant, `debug_me {}` now shows the receiver class's value. Also review [debug_me#51](https://github.com/MadBomber/debug_me/pull/51) (test-only, but it deletes two never-failing tests, so it was not self-merged). (added 2026-11-12)
- [ ] [debug_me#46](https://github.com/MadBomber/debug_me/issues/46) (bump actions/checkout and setup-python off Node 20) needs a push with `workflow` scope; bot tokens lack it. Push it yourself, or grant the scope. (added 2026-11-12)

## typed_bus

- [ ] Review and merge into `develop` the three bomberbot PRs from 2026-11-12 (none self-merged: two touch `lib/`, one rewrites a released CHANGELOG heading). All CI green; they merge cleanly together: [#30](https://github.com/MadBomber/typed_bus/pull/30) (a publish blocked on backpressure or throttle no longer delivers after `close`, F-15), [#29](https://github.com/MadBomber/typed_bus/pull/29) (`drain` with `break` no longer re-queues the entry it just handled, F-49, follow-up to #23), [#28](https://github.com/MadBomber/typed_bus/pull/28) (CHANGELOG: drain bullet matches #23, LICENSE note, `[0.1.0]` heading → `[0.0.1] - 2026-02-07` with the rename and throttle bullets moved under it). (added 2026-11-12)
- [ ] At the next release, close the issues fixed on `develop` (#1-#6, #10-#21) if `Fixes #N` doesn't do it; those keywords only auto-close on merge to `main`. BomberBot left a "fixed on develop in <sha>" note on each. (added 2026-11-11, updated 2026-11-12)
- [ ] Enable Dependabot vulnerability alerts in repo settings (still off: `dependabot/alerts` returns 403). `.github/dependabot.yml` now exists (4f26122), but the alerts toggle is admin-only. (added 2026-11-10)

## agent99

- [ ] Review and merge into `develop` [#27](https://github.com/MadBomber/agent99/pull/27) (Fixes #20, lib), [#28](https://github.com/MadBomber/agent99/pull/28) (Fixes #21, #15, lib) and [#29](https://github.com/MadBomber/agent99/pull/29) (Fixes #23, examples). Their CHANGELOG lines don't collide, so any merge order works. (added 2026-11-12)
- [ ] Issues #3/#4/#5… won't auto-close: `Fixes #N` only fires on merges into `main`. Close #4 by hand now (fixed on develop via #9) or at release. Now also #3, #5, #6, #8 (fixed on develop by #10–#13). (added 2026-11-10, updated 2026-11-12)
- [ ] The agent99 docs site is 404 (GitHub Pages disabled, F-25). Before re-enabling Pages, handle the held-local note S-6 for this repo (same one-line `mkdocs.yml` fix as smart_message#9), then redeploy. (added 2026-11-10)

## trak_flow

- [ ] Create a `develop` branch in trak_flow (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#1](https://github.com/MadBomber/trak_flow/issues/1)–[#3](https://github.com/MadBomber/trak_flow/issues/3) and findings S-2, F-3, C4 are ready to become PRs. Third repo with this gap (after typed_bus, agent99). (added 2026-11-09)

## smart_message

- [ ] Review [#21](https://github.com/MadBomber/smart_message/pull/21) (test), [#22](https://github.com/MadBomber/smart_message/pull/22) (docs) and [#23](https://github.com/MadBomber/smart_message/pull/23) (lib). The queue is full, so bomberbot opens no new smart_message PRs until some merge. #21 and #23 both add CHANGELOG `[Unreleased]` blocks; the second to merge needs a keep-both rebase. (added 2026-11-12) After #21 merges, close #14 by hand: `Fixes #14` auto-closes only on merges to `main`. (noted 2026-11-12 visit 7)
- [ ] Close #6, #7, #8, #12, #13, #15, #16, #17 when you see fit: fixed on develop by 07af3d4 (BomberBot commented on each). (added 2026-11-12)
- [ ] Decide F-67/F-75/F-76 (public API with no callers) before 0.1.0 ships: drop them and list them in the upgrade guide, or keep them. List in `bomberbot/findings/smart_message.md`. (added 2026-11-12)

## gem-skill

- [ ] Review and merge into `develop` [gem-skill#10](https://github.com/MadBomber/gem-skill/pull/10) (bundle flag parsing, F-3/F-4; lib change, so not low risk). Note the behaviour change: `bundle skill` now exits 1 on an unknown, abbreviated or value-less flag, where before it silently ignored it. (added 2026-11-10)
- [ ] Review [gem-skill#9](https://github.com/MadBomber/gem-skill/pull/9) (tests only, F-12). Not merged as low risk because its review loop hit the 3-round cap. Optional: `~/sandbox/git_repos/madbomber/gem-skill/.claude/skills/my_gem` in your checkout is the dangling link this leak left on 2026-09-24; it's safe to `rm`. (added 2026-11-10)
- [ ] Review and merge [gem-skill#11](https://github.com/MadBomber/gem-skill/pull/11) (verify truncation fix, F-25; lib change, not low risk). Fixes #6. One-line guard so a truncated `--verify` reply no longer overwrites the cached skill. (added 2026-11-10)
- [ ] Review and merge [gem-skill#12](https://github.com/MadBomber/gem-skill/pull/12) (exit codes, F-26; lib change, not low risk). Fixes #7. Behaviour change: `gem skill` now exits 1 on error paths (unknown subcommand, missing name, failed per-gem install/verify) instead of 0; error (1) takes precedence over verify-fixed (2). (added 2026-11-10)
- [ ] Review and merge [gem-skill#13](https://github.com/MadBomber/gem-skill/pull/13) (S-1 path validation, neutral title; lib change, not low risk). Adds a `Gem::Skill::Validation` module and applies it at the lockfile/cache/linker boundaries. Security context is in the bomberbot final report of 2026-11-10, not in the PR. (added 2026-11-10)

## scripts

- [ ] Close [scripts#19](https://github.com/MadBomber/scripts/issues/19) and [scripts#23](https://github.com/MadBomber/scripts/issues/23) by hand, or let them close when develop reaches main: their fixes merged to develop via #25 and #26, and `Fixes` only auto-closes on main. (added 2026-11-10, updated 2026-11-12)
- [ ] Before the next develop → main release, sync the two: main has e746bef (`ruler` rewrite, 2026-11-11) that develop lacks. (added 2026-11-12)
- [ ] Delete merged bot branches on MadBomber/scripts if you want them gone: `bomber/*` (9, plus `bomber/readme-fix-rows` and `bomber/readme-index-complete`, whose content reached develop through #25), `bomberbot/cp-r-spaces`, `bomberbot/land-readme-index`, `bomberbot/sinatra-bind-error`, and the orphan `dependabot/bundler/templates/sinatra_app/tzinfo-1.2.11`. (added 2026-11-12)
- [ ] Review [scripts#37](https://github.com/MadBomber/scripts/pull/37) (S-2 fix, neutral title; details in `notes/security/scripts.md`). After merge, the next run of `backup_envrc.rb` changes your existing `~/.envrc_backup` to mode 0700, and a failed copy now exits 1 without advancing the timestamp. (added 2026-11-12)
- [ ] Review [scripts#35](https://github.com/MadBomber/scripts/pull/35) (shc.rb file arg, Fixes #32) and [scripts#36](https://github.com/MadBomber/scripts/pull/36) (yaml2json aliases, Fixes #30). One-line code changes, so not self-merged. (added 2026-11-12)
- [ ] scripts: S-3, S-4, S-5 and new S-6, S-7, S-8 are held locally in `notes/security/scripts.md`; S-8 (medium-low) is the one to read first. (added 2026-11-10, updated 2026-11-12)

## sqa

- [ ] Review [sqa#53](https://github.com/MadBomber/sqa/pull/53) (test CI on Ruby 3.4/4.0, green). Optional: make `test (3.4)`/`test (4.0)` required checks. Once it merges, later PRs get CI. (added 2026-11-12)
- [ ] Review [sqa#54](https://github.com/MadBomber/sqa/pull/54) (docs: backtest fractions as percentages). After merge the Pages site redeploys from develop; check the Backtesting page. (added 2026-11-12)
- [ ] Review [sqa#58](https://github.com/MadBomber/sqa/pull/58) (Alpha Vantage/Stooq queries from a params hash; lib). See the 2026-11-12 final report for context. (added 2026-11-12)
- [ ] Decide the design for [sqa#4](https://github.com/MadBomber/sqa/issues/4) (API keys in config: env var name vs value vs close); see inbox. (added 2026-11-10)

## robot_lab

- [ ] Merge `main` into `develop`. `develop` (now 9d407dd, VERSION 0.2.6) is 31 commits behind `main` (0.6.0) and a few ahead (bomberbot merges), so bomberbot PRs land on stale code; bomberbot may not push `develop` itself. Until then it only builds changes in files that are identical on both branches. Extra reason to do this soon: develop's stale gemspec still pins `ruby_llm "~> 1.12"`, which resolves to a version carrying 3 published High ReDoS CVEs (main 0.6.0 is already on ruby_llm 2.1.1 and audits clean), so the sync also clears that. (added 2026-11-10, CVE note 2026-11-10 v2)
- [ ] Review [robot_lab#40](https://github.com/MadBomber/robot_lab/pull/40) (budget leak), [#39](https://github.com/MadBomber/robot_lab/pull/39) (SKILL.md front matter) and [#38](https://github.com/MadBomber/robot_lab/pull/38) (`ToolManifest#count`). All three are independent `lib/` fixes on sync-safe files. (added 2026-11-12)
- [ ] Close [robot_lab#33](https://github.com/MadBomber/robot_lab/issues/33) by hand (fixed on develop by #34, 9d407dd), or let it close when develop reaches main. If you pull develop into a checkout that still has `.claude/settings.local.json` tracked, back that file up first (#27 untracked it). (added 2026-11-12)
- [ ] Decide how to fix [robot_lab#43](https://github.com/MadBomber/robot_lab/issues/43): released 0.6.0 breaks with ruby_llm 2.2.0 on tool approvals / MCP input. Options in inbox; the fix must ship from `main` (bomberbot can't). (added 2026-11-12 04:18)
- [ ] Release the `robot_lab-*` extension gems with a core requirement that admits 0.6.0 ([robot_lab#29](https://github.com/MadBomber/robot_lab/issues/29)). (added 2026-11-10)

## smart_message-transport-named_pipes

- [ ] Review [#27](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/27) (Fixes #17: connect after disconnect; lib) and [#22](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/22) (Fixes #19: README Related Projects; docs, held from self-merge only by an out-of-scope review note). Close #17/#19 by hand after merge. (added 2026-11-12 visit 5)
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

- [ ] Review [simple_flow#24](https://github.com/MadBomber/simple_flow/pull/24) (Fixes #18; lib change, so not merged as low risk). Intended behaviour change: a parallel branch that returns a fresh `Result` without errors no longer drops the incoming errors. (added 2026-11-12)
- [ ] Review [simple_flow#25](https://github.com/MadBomber/simple_flow/pull/25) and [simple_flow#26](https://github.com/MadBomber/simple_flow/pull/26) (docs only, Fixes #20 and #19). Not self-merged because `deploy-github-pages.yml` publishes the docs site on `docs/**` pushes to `develop`. (added 2026-11-12)

## smart_message-transport-lanet

- [ ] Decide F-1: the transport calls a Lanet API that doesn't exist. Options are in inbox.md (2026-11-10). Issue #3 and most of the backlog depend on this. (added 2026-11-10)
- [ ] Security: the first audit found S-1…S-7, held locally in `notes/security/smart_message-transport-lanet.md`. All are latent while F-1 stands, and the gem is unpublished, but any F-1 rewrite must cover S-1…S-4. S-6 is cleared by [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13). (added 2026-11-10, updated 2026-11-12)
- [ ] Decide F-19: should a node receive its own published messages? `publish_to_nodes` never delivers locally. (added 2026-11-10)
- [ ] Decide the Ruby floor (F-24): the gemspec says `>= 3.2.0` and `.rubocop.yml` targets 3.2, but no lock installs on 3.2, and smart_message's dependency chain (breaker_machines ≥ 0.18, chrono_machines ≥ 0.9.1) now needs Ruby ≥ 4.0. Options: floor 3.3 and keep holding those gems back (#13 does this), or floor 4.0 and drop 3.x from CI. (added 2026-11-10, updated 2026-11-12)
- [ ] Optional: turn on "Automatically delete head branches" for this repo (merged bot branches pile up; the tender deletes them by hand). (added 2026-11-12)
- [ ] Before merging your [#29](https://github.com/MadBomber/smart_message-transport-lanet/pull/29) (constructor ordering), read the 2026-11-13 S-4 entry in local `notes/security/smart_message-transport-lanet.md`, and merge [#27](https://github.com/MadBomber/smart_message-transport-lanet/pull/27) first (F-51). Merge-order notes for #20–#29 are in inbox.md. (added 2026-11-13)
- [ ] Optional, from the #13 merge: make the breaker_machines hold durable with `gem "breaker_machines", "~> 0.17.0", force_ruby_platform: true`, and make `test (4.0)` a required check. (added 2026-11-12, carried 2026-11-13)

## htm (2026-11-11)

- [ ] Review [htm#32](https://github.com/MadBomber/htm/pull/32) (Fixes #19; adds `NodeTag.link`, which restores a soft-deleted link or creates one, fixes `NodeTag#restore!`, and swaps the six `find_or_create` call sites). Touches `lib/`, so not self-merged. 652 tests green. (added 2026-11-11)
- [ ] Review [htm#31](https://github.com/MadBomber/htm/pull/31) (makes `HTM_PROPOSITION__ENABLED=false` actually disable extraction; config coercion plus test). Touches `lib/htm/config.rb`, so not self-merged. 648 tests green. (added 2026-11-11)
- [ ] Review [htm#30](https://github.com/MadBomber/htm/pull/30) (`htm_mcp help` text: `HTM_CHUNKING__CHUNK_SIZE/CHUNK_OVERLAP`, the proposition default, the `fiber` backend and the connection timeout; plus a test). Touches `lib/htm/mcp/cli.rb`, so not self-merged. 650 tests green. (added 2026-11-11)
- [ ] Optional: turn on "Automatically delete head branches" for htm. Merged bot branches pile up otherwise; this visit deleted ten. (added 2026-11-11)
- [ ] smart_message: close issues [#6](https://github.com/MadBomber/smart_message/issues/6), #7, #8, #12, #13, #15, #16 and #17. Each is fixed on `develop` by your 0.1.0 commit 07af3d4; BomberBot commented with evidence on each. `Fixes #N` only auto-closes from `main`. (added 2026-11-12)
- [ ] smart_message: the live docs site still serves `main`'s old `mkdocs.yml` (the script #9 removed). It goes away when you merge `develop` → `main` for the 0.1.0 release, or cherry-pick ffa364f to `main` sooner. (added 2026-11-12)
- [ ] Review [smart_message#21](https://github.com/MadBomber/smart_message/pull/21) (test-only, Fixes #14) and [smart_message#22](https://github.com/MadBomber/smart_message/pull/22) (2-line DLQ docs fix). Both green; review loops hit the 3-round cap, so not self-merged. (added 2026-11-12)
- [ ] Review [smart_message#23](https://github.com/MadBomber/smart_message/pull/23) (DLQ replay rebuilds an entry as its stored class; `lib/`). Details in the 2026-11-12 bomberbot final report. (added 2026-11-12)

## shared_tools (2026-11-12)

- [ ] Optional, after merging [#9](https://github.com/MadBomber/shared_tools/pull/9): make `test (3.3)`, `test (3.4)` and `test (4.0)` required status checks on `develop` and `main`.

## dev (2026-11-12)

- [ ] Review [dev#10](https://github.com/MadBomber/dev/pull/10) (weekly_report snaps `STATUS_WEEK_OF` to Monday) and [dev#11](https://github.com/MadBomber/dev/pull/11) (`reek_baseline` task). Merge #11 before [dev#12](https://github.com/MadBomber/dev/pull/12) (absent flay/reek/flog/bundler-audit report SKIP), which is stacked on it; the next tender retargets #12 to `develop`. No CI on this repo, so no checks gate them.
- [ ] `main` is 26 commits behind `develop` (unchanged since 2026-10-30) and still has `schedule.loki`, which fails to load under asgard 0.6.0. Your checkout `~/sandbox/git_repos/madbomber/dev` is on `main`, so every `import_up "dev/..."` consumer runs the old files. Merge `develop` into `main`, or switch that checkout to `develop`.
- [ ] Your checkout has an uncommitted `release` guard in `gem_tasks.loki`; [dev#7](https://github.com/MadBomber/dev/issues/7) covers the same ground. Bomberbot left `gem_tasks.loki` alone this visit so it won't conflict.

## kbs (2026-11-12)

- [ ] Merge [#28](https://github.com/MadBomber/kbs/pull/28) with a **merge commit** (not squash or rebase), or close it and sync `main` into `develop` yourself. It brings Copilot's #19-#27 to `develop` with 7 conflicts resolved and keeps develop's Redis test opt-in. (added 2026-11-12)
- [ ] Review [#33](https://github.com/MadBomber/kbs/pull/33) (decompiler inlines captured scalar values). Its review loop hit the 3-round cap and the last fixes got no fourth round, so it was not self-merged. (added 2026-11-12)
- [ ] Review [#32](https://github.com/MadBomber/kbs/pull/32) (`gem "csv"` in the Gemfile). Not self-merged because it touches the Gemfile. (added 2026-11-12)
- [ ] `bomber/knowledge-base-engine` still holds your post-merge commit `c53e503` (trims the #10 CHANGELOG line); it never reached `develop`. Keep it or delete the branch. `bomber/leading-negation` (no PR) is superseded by Copilot #23; delete it when you like. (added 2026-11-12)
