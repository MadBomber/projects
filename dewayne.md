# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## asgard

- [ ] Review [asgard#20](https://github.com/MadBomber/asgard/pull/20) (README, CHANGELOG and `docs/files.md` list your new `truncate_log`). Docs only, green, but not merged as low risk: merging deploys the docs site (Pages runs on `docs/**` pushes to `develop`). (added 2026-11-13)
- [ ] Review [asgard#21](https://github.com/MadBomber/asgard/pull/21) (the dependency-arity error names the declaring task, `(needed by c)`, same wording as your 621df2e for #14). Lib change. Merges cleanly alongside #20. (added 2026-11-13)
- [ ] Decide [asgard#17](https://github.com/MadBomber/asgard/issues/17): close it as done by `truncate_log --lines`, or keep it open for `--days N` / `changes/*.json` cleanup / `log_retention`? (added 2026-11-13)

## aia

- [ ] Let bots push GitHub Actions workflows: `gh auth refresh -h github.com -s workflow` (the token lacks `workflow` scope), or add aia's test CI yourself from `bomberbot/plans/aia-test-ci.md`. aia has no test CI today. (added 2026-11-13)
- [ ] Decide whether aia should require `ruby_llm >= 2.2` (see local security notes for aia, S-5, after #219). (added 2026-11-13)
- [ ] Decide how aia's `develop` gets main's 6 post-release commits, including the `--paint` fix (F-45/F-46; options in inbox.md). (added 2026-11-12)

## ragdoll

- [ ] Nothing pending for ragdoll; open questions are in inbox.md (2026-11-13).

## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23) is merged (Ruby 3.2 floor confirmed). At release: dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer; add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08, updated 2026-11-09)
- [ ] Decide debug_me S-36 and S-37; the details are in your local `notes/security/debug_me.md` (S-36 re-confirmed on develop 733fe6d, 2026-11-13). (added 2026-11-12, updated 2026-11-13)
- [ ] Review [debug_me#61](https://github.com/MadBomber/debug_me/pull/61) (tests only: first coverage of `time:`/`strftime:`, mutation-proven). Not self-merged because its `/code-review` loop hit the 3-round cap. Also review [debug_me#63](https://github.com/MadBomber/debug_me/pull/63) (lib/: BasicObject values print instead of raising NoMethodError; review hit the cap, the seven unfixed round-3 items are explained in the body). (added 2026-11-13)

## typed_bus

- [ ] Review and merge into `develop` the three bomberbot PRs from 2026-11-13 (all CI green, review clean, none self-merged: two change what the docs site deploys, one touches `lib/`): [#37](https://github.com/MadBomber/typed_bus/pull/37) (Requirements list `logger`, LICENSE linked, mkdocs copyright 2026), [#38](https://github.com/MadBomber/typed_bus/pull/38) (delete unused 16.6 MB gif), [#39](https://github.com/MadBomber/typed_bus/pull/39) (Unknown channel error shows the inspected name and registered channels). After merge of #37 and a release, the docs/index.md LICENSE link can point to `main`. (added 2026-11-13)
- [ ] typed_bus: turn on Dependabot alerts in repo settings (alerts API returns 403); see [#40](https://github.com/MadBomber/typed_bus/issues/40). (added 2026-11-13)
- [ ] At the next release, close the issues fixed on `develop` (#1-#6, #10-#21) if `Fixes #N` doesn't do it; those keywords only auto-close on merge to `main`. BomberBot left a "fixed on develop in <sha>" note on each. (added 2026-11-11, updated 2026-11-12)
- [ ] Enable Dependabot vulnerability alerts in repo settings (still off: `dependabot/alerts` returns 403). `.github/dependabot.yml` now exists (4f26122), but the alerts toggle is admin-only. (added 2026-11-10)

## agent99

- [ ] Review and merge into `develop` [#55](https://github.com/MadBomber/agent99/pull/55) (F-55, lib), [#56](https://github.com/MadBomber/agent99/pull/56) (F-65+F-71, lib) and [#57](https://github.com/MadBomber/agent99/pull/57) (F-59, deletions in lib). Their CHANGELOG lines sit above where your #38–#53 append, so they merge in any order without adding to those conflicts. (added 2026-11-13)
- [ ] Issues #3/#4/#5… won't auto-close: `Fixes #N` only fires on merges into `main`. Close #4 by hand now (fixed on develop via #9) or at release. Now also #3, #5, #6, #8 (fixed on develop by #10–#13). (added 2026-11-10, updated 2026-11-12)
- [ ] The agent99 docs site is 404 (GitHub Pages disabled, F-25). Before re-enabling Pages, handle the held-local note S-6 for this repo (same one-line `mkdocs.yml` fix as smart_message#9), then redeploy. (added 2026-11-10)

## trak_flow

- [ ] Create a `develop` branch in trak_flow (from `main`), or tell bomberbot to target `main` there. Until then bomberbot can only file issues; [#1](https://github.com/MadBomber/trak_flow/issues/1)–[#3](https://github.com/MadBomber/trak_flow/issues/3) and findings S-2, F-3, C4 are ready to become PRs. Third repo with this gap (after typed_bus, agent99). (added 2026-11-09)

## smart_message

- [ ] Review [#54](https://github.com/MadBomber/smart_message/pull/54) (examples: delete three unused dashboard builders and leftovers; or say "wire them in"), [#55](https://github.com/MadBomber/smart_message/pull/55) (tests for documented DLQ API; 6 commits, squash fine) and [#56](https://github.com/MadBomber/smart_message/pull/56) (tests drain the dispatcher instead of sleeping). All test/examples-only, no CHANGELOG line, green. (added 2026-11-13)
- [ ] Your #36–#50 conflict with each other once merging starts: CHANGELOG in 79 of 105 pairs, plus #39×#40 (`packaging_test`), #41×#42 and #43×#44 (`configuration.rb`), #44/#45/#48 (`multi_transport_test`). All 15 merged together pass 466/0F (responder's local merge). `Closes #N` won't auto-close on `develop`, so close #18–#35 by hand as they land. On #20, decide whether #46 should also reject an undefined handler class (BomberBot commented). (added 2026-11-13)
- [ ] Decide F-67/F-75/F-76 (public API with no callers) before 0.1.0 ships: drop them and list them in the upgrade guide, or keep them. List in `bomberbot/findings/smart_message.md`. (added 2026-11-12)

## gem-skill

- [ ] Review and merge into `develop` [gem-skill#26](https://github.com/MadBomber/gem-skill/pull/26) (verify honours `--max-tokens`/`GEMSKIL_MAX_TOKENS`, F-17; lib change, not low risk). Also drops the Apfel docs advice to skip `--verify`. Its CHANGELOG line will conflict with your #20-#24 once they merge; a trivial rebase. (added 2026-11-13)
- [ ] Review and merge into `develop` [gem-skill#36](https://github.com/MadBomber/gem-skill/pull/36) (Fixes #28, missing Gemfile.lock), [gem-skill#32](https://github.com/MadBomber/gem-skill/pull/32) (Fixes #29, setup exit status) and [gem-skill#31](https://github.com/MadBomber/gem-skill/pull/31) (gemspec doc/bug-tracker URIs). All lib/gemspec, so not low risk; repo has no test CI, proof is the local suite. #36 merges clean with your #23. (added 2026-11-13)

## scripts

- [ ] Sync main and develop both ways: main has e746bef (`ruler` rewrite, committed straight to main) that develop lacks, and develop has #51-#67 that main lacks (#68 merged develop when it held only #50). Merge main into develop, then open a develop → main PR. (added 2026-11-12, updated 2026-11-13)
- [ ] Delete merged bot branches on MadBomber/scripts if you want them gone: `bomber/*` (9, plus `bomber/readme-fix-rows` and `bomber/readme-index-complete`), `bomberbot/cp-r-spaces`, `bomberbot/land-readme-index`, `bomberbot/sinatra-bind-error`, `madbot/shc-file-arg`, `madbot/yaml2json-aliases`, `madbot/backup-envrc-fileutils`, and the orphan `dependabot/bundler/templates/sinatra_app/tzinfo-1.2.11`. (added 2026-11-12, updated 2026-11-13)
- [ ] Review [scripts#69](https://github.com/MadBomber/scripts/pull/69) (S-9, removes the vendored `printenv`; neutral title, details in `notes/security/scripts.md`). After merge and a pull of `~/scripts`, `printenv` resolves to `/usr/bin/printenv`. (added 2026-11-13)
- [ ] Review [scripts#70](https://github.com/MadBomber/scripts/pull/70) (resize_images full path, F-89) and [scripts#71](https://github.com/MadBomber/scripts/pull/71) (fix_perms skips `.git`, F-81). Small code changes, so not self-merged. (added 2026-11-13)
- [ ] scripts: S-3 to S-8 are held locally in `notes/security/scripts.md`; S-8 (medium-low) is the one to read first. (added 2026-11-10, updated 2026-11-12)

## sqa

- [ ] [sqa#53](https://github.com/MadBomber/sqa/pull/53) merged; optional after-merge step: make `test (3.4)`/`test (4.0)` required checks on develop. (added 2026-11-12)
- [ ] Decide the design for [sqa#4](https://github.com/MadBomber/sqa/issues/4) (API keys in config: env var name vs value vs close); see inbox. (added 2026-11-10)
- [ ] Review [sqa#84](https://github.com/MadBomber/sqa/pull/84) (PatternMatcher empty clusters keep their own centroid; lib, CHANGELOG line may need a rebase after your #68). (added 2026-11-13)
- [ ] Review [sqa#85](https://github.com/MadBomber/sqa/pull/85) (adds `irb` as a **runtime** dependency so the shipped `sqa-console` runs on Ruby 4). (added 2026-11-13)
- [ ] sqa security S-3/S-4: see the 2026-11-13 final report / local notes for disclosure and fix decisions. (added 2026-11-13)

## robot_lab

- [ ] Merge `main` into `develop`. `develop` (now c700ba6, VERSION 0.2.6) is 31 commits behind `main` (0.6.0) and a few ahead (bomberbot merges), so bomberbot PRs land on stale code; bomberbot may not push `develop` itself. Until then it only builds changes in files that are identical on both branches. Extra reason to do this soon: develop's stale gemspec still pins `ruby_llm "~> 1.12"`, which resolves to a version carrying 3 published High ReDoS CVEs (main 0.6.0 is already on ruby_llm 2.1.1 and audits clean), so the sync also clears that. (added 2026-11-10, CVE note 2026-11-10 v2)
- [ ] Review [robot_lab#70](https://github.com/MadBomber/robot_lab/pull/70) (`Ledger#reserve!` Float rounding refuses exactly the remaining budget; `lib/`, sync-safe). (added 2026-11-13)
- [ ] Close [robot_lab#33](https://github.com/MadBomber/robot_lab/issues/33) by hand (fixed on develop by #34, 9d407dd), or let it close when develop reaches main. If you pull develop into a checkout that still has `.claude/settings.local.json` tracked, back that file up first (#27 untracked it). (added 2026-11-12)
- [ ] Decide how to fix [robot_lab#43](https://github.com/MadBomber/robot_lab/issues/43): released 0.6.0 breaks with ruby_llm 2.2.0 on tool approvals / MCP input. Options in inbox; the fix must ship from `main` (bomberbot can't). (added 2026-11-12 04:18)
- [ ] Release the `robot_lab-*` extension gems with a core requirement that admits 0.6.0 ([robot_lab#29](https://github.com/MadBomber/robot_lab/issues/29)). (added 2026-11-10)

## smart_message-transport-named_pipes

- [ ] Review [#39](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/39) (gemspec: stop packaging `.envrc`/`.github`/`Rakefile`, drop dead `exe` lines, read VERSION from version.rb as text; gemspec → not low risk). (added 2026-11-13)
- [ ] Note: [#21](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/21) (`unsubscribe!`) was merged into its stacked base `madbot/unsubscribe-key`, never `develop`; your [#34](https://github.com/MadBomber/smart_message-transport-named_pipes/pull/34) carries the same change, so merge #34 to land it. Consider turning on "Automatically delete head branches" in repo settings so GitHub retargets stacked PRs. (added 2026-11-13)
- [ ] Decide F-24/F-14: README Complete Example and both examples use `attribute :x, :type`, which no smart_message provides; rewrite them to the verified framework path (`from`/`property`/`config { transport }`/`Msg.subscribe`)? Also F-26 (gem never released, docs say it is) and F-30 (throughput claim 10–100× off). Options in `inbox.md`. (added 2026-11-12)

- [ ] Decide F-2: remove the public `attr_reader :publisher_pipes` (always `{}`, never populated — the publisher `.out.pipe` side was never wired up)? It's public API, so removal needs a deprecation cycle. The dead *private* method is already gone via #8. (added 2026-11-10)
- [ ] Decide F-3: the documented config options `:mode` and `:buffer_size` are inert (never read in lib). Implement them or remove them from `DEFAULT_CONFIG`/YARD/README? Both are documented public options. (added 2026-11-10)
- [ ] Decide F-10: message framing uses `pipe.puts`/`pipe.gets` + `strip`, so a serialized payload containing a newline is truncated at the first `\n`. Single-line JSON is fine today; a robust fix (length-prefix or escaping) is a wire-format change. Implement or document the single-line constraint? (added 2026-11-10)

- [ ] Decide the `smart_message` dependency floor for [#6](https://github.com/MadBomber/smart_message-transport-named_pipes/issues/6): lower to `>= 0.0.17` or publish `smart_message` 0.1.0 first. The gem is uninstallable until this is resolved (and CI in #7 can't use `bundle` until then). (added 2026-11-10) **Update 2026-11-13 (F-46/F-47):** smart_message 0.1.0 now exists on GitHub `develop` (not RubyGems, untagged) and this transport works against it, so the `>= 0.1.0` floor is right once 0.1.0 is published; but 0.1.0 requires Ruby `>= 4.0`, so this gem's `>= 3.2.0` floor, README and `.rubocop.yml` should move to 4.0 too.
- [ ] Decide F-14 (README teaches a transport API the real smart_message rejects), F-18 (#6 also needs smart_message's own runtime deps fixed), F-19 (Ruby floor 3.3 vs 4.0). Options in `inbox.md` (visit 3). (added 2026-11-10)
- [ ] Security: S-1..S-8 are held locally in `notes/security/smart_message-transport-named_pipes.md` (S-8 added 2026-11-13). None remotely exploitable; your call on hardening/disclosure. (added 2026-11-10, reworded 2026-11-13 to keep details out of public brain)

## simple_flow

- [ ] Review [simple_flow#40](https://github.com/MadBomber/simple_flow/pull/40) and [simple_flow#41](https://github.com/MadBomber/simple_flow/pull/41) (lib fixes, so not self-merged; they merge together cleanly). #40 makes a Float `max_concurrent` such as `1.5` raise too. #41 fixes a regression our #24 introduced. (added 2026-11-13)
- [ ] Review [simple_flow#44](https://github.com/MadBomber/simple_flow/pull/44) (Fixes #36, lib) and [simple_flow#42](https://github.com/MadBomber/simple_flow/pull/42) (Fixes #38, docs, deploys from develop). Merge #40 before [simple_flow#43](https://github.com/MadBomber/simple_flow/pull/43) (Fixes #37, stacked on #40); the next visit's tender retargets #43 to develop once #40 lands. (added 2026-11-13)

## smart_message-transport-lanet

- [ ] Decide F-1: the transport calls a Lanet API that doesn't exist. Options are in inbox.md (2026-11-10). Issue #3 and most of the backlog depend on this. (added 2026-11-10)
- [ ] Security: the first audit found S-1…S-7, held locally in `notes/security/smart_message-transport-lanet.md`. All are latent while F-1 stands, and the gem is unpublished, but any F-1 rewrite must cover S-1…S-4. S-6 is cleared by [#13](https://github.com/MadBomber/smart_message-transport-lanet/pull/13). (added 2026-11-10, updated 2026-11-12)
- [ ] Decide F-19: should a node receive its own published messages? `publish_to_nodes` never delivers locally. (added 2026-11-10)
- [ ] Decide the Ruby floor (F-24): the gemspec says `>= 3.2.0` and `.rubocop.yml` targets 3.2, but no lock installs on 3.2, and smart_message's dependency chain (breaker_machines ≥ 0.18, chrono_machines ≥ 0.9.1) now needs Ruby ≥ 4.0. Options: floor 3.3 and keep holding those gems back (#13 does this), or floor 4.0 and drop 3.x from CI. (added 2026-11-10, updated 2026-11-12)
- [ ] Optional: turn on "Automatically delete head branches" for this repo (merged bot branches pile up; the tender deletes them by hand). (added 2026-11-12)
- [ ] Before merging your [#29](https://github.com/MadBomber/smart_message-transport-lanet/pull/29) (constructor ordering), read the 2026-11-13 S-4 entry in local `notes/security/smart_message-transport-lanet.md`, and merge [#27](https://github.com/MadBomber/smart_message-transport-lanet/pull/27) first (F-51). Merge-order notes for #20–#29 are in inbox.md. (added 2026-11-13)
- [ ] Optional, from the #13 merge: make the breaker_machines hold durable with `gem "breaker_machines", "~> 0.17.0", force_ruby_platform: true`, and make `test (4.0)` a required check. (added 2026-11-12, carried 2026-11-13)
- [ ] Optional, after [#32](https://github.com/MadBomber/smart_message-transport-lanet/pull/32) merges: have CI run the default `bundle exec rake` (not just `rake test`) so the RuboCop baseline is enforced, and drop the stale "pre-existing offenses" comment in `ci.yml`. Bots can't push `.github/workflows` changes (no `workflow` scope).
- [ ] Optional, with [#32](https://github.com/MadBomber/smart_message-transport-lanet/pull/32): add `.rubocop_todo.yml` to the gemspec's `spec.files` reject list (`.rubocop.yml` is excluded; the todo file isn't).

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

## dev (2026-11-13)

- [ ] Review [dev#22](https://github.com/MadBomber/dev/pull/22) (branch_report keeps git's `fatal:` lines off stderr) and [dev#25](https://github.com/MadBomber/dev/pull/25) (one-line abort on a bad `STATUS_DATE` / `STATUS_WEEK_OF`). No CI, so no checks gate them.
- [ ] Merge [dev#22](https://github.com/MadBomber/dev/pull/22) **before** [dev#26](https://github.com/MadBomber/dev/pull/26) (`|` in branch/author names), which is stacked on it; then retarget #26 to `develop` (or let the next visit do it).
- [ ] Decide on [dev#25](https://github.com/MadBomber/dev/pull/25): should `STATUS_DATE` / `STATUS_WEEK_OF` accept only YYYY-MM-DD? Today words like `tuesday` silently become a nearby date; #25 keeps that.
- [ ] `main` is 36 commits behind `develop` (unchanged since 2026-10-30) and still has `schedule.loki`, which fails to load under asgard 0.6.0. Your checkout `~/sandbox/git_repos/madbomber/dev` is on `main`, so every `import_up "dev/..."` consumer runs the old files. Merge `develop` into `main`, or switch that checkout to `develop`.

## kbs (2026-11-13)

- [ ] `bomber/knowledge-base-engine` still holds your post-merge commit `c53e503` (trims the #10 CHANGELOG line); it never reached `develop`. Keep it or delete the branch. `bomber/leading-negation` (no PR) is superseded by Copilot #23; delete it when you like. (added 2026-11-12)
- [ ] Review and merge [#37](https://github.com/MadBomber/kbs/pull/37) (`rule "x", priority: N` keyword, Fixes #29). Touches `lib/`, so not self-merged. (added 2026-11-13)
- [ ] Review and merge [#39](https://github.com/MadBomber/kbs/pull/39) (gem ships only lib/ + README/CHANGELOG/LICENSE, 314 KB to 31 KB, Fixes #31). Changes the published package, so not self-merged. At release, add a CHANGELOG line for it (left out to avoid a conflict with #37). (added 2026-11-13)

