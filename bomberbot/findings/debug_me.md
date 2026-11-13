# Findings: debug_me

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-12 | Second audit, develop 0d7fb14 (develop now contains all of main). bundler-audit clean. Findings 38-39, S-36, S-37. |
| dead code | 2026-11-12 | Second visit: improver read 0d7fb14..200c10f, nothing removable; two never-failing tests (#45) PR'd. 2026-11-12: improver read 3a4651e..0d7fb14 and lib/ in full; nothing in lib/ removable; two never-failing tests found (#45 below). Quick scans through 3c1bd45; improver reads of 3c1bd45..77b8c25, 77b8c25..7e12714, 7e12714..6a9ec45 (nothing removable each time). 2026-11-09: read 6a9ec45..e4858c9 and the whole tree — found the unused `WorldView#everything` test helper, shipped as #36 (merged). |
| dependencies | 2026-11-12 | Full audit, develop 0d7fb14. `bundle outdated`: only minitest 5.27 → 6.1, held by gemspec `~> 5.0` (#8; suite passes on 6.1.0). Finding 40 (actions/checkout@v4 on Node 20, issue #46), 41 (Ruby 3.2 floor EOL, needs decision). |
| adoption | 2026-11-13 | develop fb0b2ea. mkdocs `--strict` clean; README/docs fences parse (bar notation); probes match docs; metadata URIs 200; gem-skill sources unchanged (14,805 chars). New: #71-#73. main now 74 behind (#53). Previous 2026-11-12 (200c10f). |
| quality | 2026-11-13 | Second quality audit, develop 733fe6d. flog 192.8 on `debug_me` (was 171.4), flay 0 in lib (92 in tests), reek 15, rubocop 56 lib / 384 lib+tests; suite green under `ruby -w` (17 test-only unused-local warnings, none from lib). New findings 64-69; #12, #34, #47, #62 fixed. Previous: 2026-11-09, develop e4858c9, findings 31-35. |

Dead-code scan last covered commit: fb0b2ea (develop, 2026-11-13 08:45; improver read 733fe6d..fb0b2ea, tests only, nothing removable)

## Findings

### 2026-11-06 adoption audit

1. **A String tag followed by other options raises ArgumentError.** `debug_me('INFO', levels: 2)` appears in getting-started, configuration (x3), and api-reference. Status: merged ([#8](https://github.com/MadBomber/debug_me/pull/8), 2026-11-07).
2. **The docs say `DebugMe.debug_me(...)` works without `include`, but it raises NoMethodError.** This is in docs/configuration.md "Without Including". There is no `module_function` or `extend self`. Fix the docs, or add `extend self` (which adds public API). Status: PR'd ([#10](https://github.com/MadBomber/debug_me/pull/10), docs fix). Making `DebugMe.debug_me` work via `extend self` still needs Dewayne's decision.
3. **api-reference names `DebugMe::VERSION`, which doesn't exist.** The real constant is `DebugMe::DEBUG_ME_VERSION`. Status: PR'd ([#10](https://github.com/MadBomber/debug_me/pull/10)).
4. **api-reference says `DEBUG_ME_MAX_BACKTRACE` defaults to 15.** The real value is 10000. Status: PR'd ([#10](https://github.com/MadBomber/debug_me/pull/10)).
5. **The api-reference "Output Format" section doesn't match real output.** Real output is `TIMESTAMP TAG Source:  file:line:in 'meth'`, then `TIMESTAMP TAG var -=> value`. Status: verified 2026-11-09 against captured output. The same wrong `TAG [TIMESTAMP] file:line` / bare-indented-var format appears in the four sync-safe onboarding pages (index.md, getting-started.md, installation.md, usage.md) and is PR'd there ([#38](https://github.com/MadBomber/debug_me/pull/38), 2026-11-09); it remains in `api-reference.md` and `configuration.md`, which are not sync-safe (fold into the main/develop sync).
6. **The README `log_me` Rails initializer example breaks when called with a block.** It passes the block positionally (`debug_me(options, block)`), which raises ArgumentError, and it overwrites its own `options` argument. Status: issue [#12](https://github.com/MadBomber/debug_me/issues/12) (with two README syntax errors).
7. **The CHANGELOG has gaps and wrong dates.**
   - CHANGELOG.md has no entry for 1.1.3 (released 2025-11-21; `$DEBUG_ME` keeps a pre-set value).
   - Its dates for 1.1.2 (2025-01-13) and 1.1.1 (2020-04-27) don't match RubyGems (2025-11-13 and 2023-11-02).
   - docs/changelog.md differs from CHANGELOG.md, and some of its content looks invented.

   Status: needs Dewayne's decision, because release headings are his.
8. **The gemspec doesn't match how tests are run.**
   - The dev dependency is `minitest ~> 5.0`, but the tests were updated for Minitest 6 and also pass on 6.1.
   - The `test_files` glob looks for `test/`, but the tests live in `tests/`.
   - The Rakefile has no `test` task, and there is no test CI workflow.

   Status: rake task and CI merged ([#9](https://github.com/MadBomber/debug_me/pull/9), 2026-11-07), plus a new gap found 2026-11-07: the suite requires `logger`, which is not a default gem on Ruby 4, so `bundle exec` runs failed until #9 added it as a dev dependency. Still open: the `test_files` glob (`test/` vs `tests/`) and the `minitest ~> 5.0` constraint.
9. **The gem package includes the docs site.** `spec.files` is `git ls-files`, so it ships docs/ (about 2000 lines plus a 127 KB jpg) and mkdocs.yml. Status: issue [#25](https://github.com/MadBomber/debug_me/issues/25) (measured 2026-11-08: 158,208 bytes, 11,776 trimmed).
10. **The `.rultor.yml` release config and `rubygems.yml.asc` look dead.** The release script's sed looks for `1.0.snapshot`, which is no longer in version.rb. Status: needs Dewayne's decision (remove, or keep for rultor).
11. **A `safe_debug`-style wrapper reports its own file and line in the header, not the caller's.** There is no option to skip a frame. Status: open idea. Adding something like a `caller_offset:` option needs Dewayne's decision, since it is new public API.

### 2026-11-07 re-check (no audit; 5 verified open findings)

- Findings 2, 3, 4 and 6 confirmed still present on develop 3055a9f. Each is a small docs fix and a good next PR.

12. **`tests/env_test.rb` restores `ENV["DEBUG_ME"]` only if its assertion passes (no `ensure`), and shells out to a bare `ruby` from PATH instead of `RbConfig.ruby`.** Raised by `/code-review` on #9. Status: fixed by #37 (env_test now uses `RbConfig.ruby` and clears the env; confirmed 2026-11-13).

### 2026-11-07 second run (no audit; found by `/code-review` on #10)

- Findings 5 and 6 confirmed still present on develop 98b8d47. Finding 5's "With Backtrace" sample is wrong too: real output is `TAG backtrace -=> [[0, "file:line:in ..."], ...]`, not `caller(0) -=>` with one frame per line.

13. **`debug_me(levels: 1) { :backtrace }` prints the backtrace twice.** With `levels > 0`, lib/debug_me.rb adds `'backtrace'` to the output list itself, then evaluates the block's own `:backtrace` again. Confirmed at runtime on develop 98b8d47. README.md says `levels` replaces `:backtrace`. Status: PR'd ([#11](https://github.com/MadBomber/debug_me/pull/11)).
14. **`levels:` is silently ignored with `header: false`, and a String `levels:` raises.** The `levels` backtrace is collected only inside `if options[:header]`, and `options[:levels] > 0` runs before `.to_i`, so `debug_me(levels: '2')` raises (ArgumentError on Ruby 4, not NoMethodError). Status: String half is issue [#13](https://github.com/MadBomber/debug_me/issues/13), merged ([#18](https://github.com/MadBomber/debug_me/pull/18), 2026-11-08); whether `levels:` should work with `header: false` needs Dewayne's decision.
15. **configuration.md's "Include at Kernel Level" heading and its "includes into Object/Kernel" comment are wrong.** A top-level `include` adds the module to Object, not Kernel. Status: issue [#27](https://github.com/MadBomber/debug_me/issues/27).
16. **api-reference's signature line still reads `debug_me(options = {}, &block)` and leaves out `**keyword_options`** (added in #8). Status: issue [#27](https://github.com/MadBomber/debug_me/issues/27).
17. **On Ruby 2.7, passing a Hash variable to `debug_me` may print the "last argument as keyword parameters" deprecation warning** since #8 added `**keyword_options`. Status: moot once [#23](https://github.com/MadBomber/debug_me/pull/23) raises the floor to 3.2.

### 2026-11-07 third run (no audit; found by `/code-review` on #11)

- Findings 5, 6, 12, 14, 15 and 16 still open on develop 3c1bd45 (not re-verified one by one; the #10 merge touched only docs, and none of these were in its scope).

18. **With `levels > 0`, an empty block or one returning `nil` no longer dumps local, instance and class variables.** `levels` puts `backtrace` in the list before the `block_value.empty?` fallback runs, so `debug_me(levels: 2) { }` shows only the backtrace, while `debug_me { }` dumps the variables. Status: open needs Dewayne's decision (is the variable dump wanted alongside `levels`?). Re-confirmed on f7041aa.

### 2026-11-08 (no audit; triage and improver)

- Issues #12, #13 and #14 re-verified on develop 77b8c25. #12 and #14 still reproduce.
- The 2026-11-07 late visit (cut off, no run entry) opened issues #12, #13 and #14 and PR #15 (merged 2026-11-08). Finding 19 records #14, which had no finding.

19. **The test suite fails with `DEBUG_ME=false` in the shell and leaves `logger_class_output.txt` behind, which keeps later runs red.** Status: issue [#14](https://github.com/MadBomber/debug_me/issues/14), PR'd ([#24](https://github.com/MadBomber/debug_me/pull/24)).
20. **`debug_me {}` in a class method, module method or class body shows no class variables or constants.** `cvar`/`cconst` read `self.class`, which is `Class` there. Verified on 77b8c25. Status: issue [#16](https://github.com/MadBomber/debug_me/issues/16).
21. **The test suite requires `amazing_print` (and the gemspec lists it) but nothing uses it; `lib/debug_me.rb` keeps a Ruby 1.8.6 `caller` branch that can't run on Ruby >= 2.3.** Verified on 77b8c25: the suite counts are unchanged without it. Status: merged ([#19](https://github.com/MadBomber/debug_me/pull/19), 2026-11-08); the Ruby floor follow-up Dewayne asked for on #17 is PR'd ([#23](https://github.com/MadBomber/debug_me/pull/23)).
22. **A top-level `debug_me {}` dumps every top-level constant** (`self.class` is Object; 429 lines in one probe for one local). Status: needs Dewayne's decision (skip constants for Object, default `cconst` off at top level, or document it). In `inbox.md`.
23. **The 11 subprocess tests in `tests/env_test.rb` fail outside a git checkout under `bundle exec`.** They inherit `-rbundler/setup`, the gemspec's `git ls-files` writes `fatal: not a git repository` to stderr, and the tests merge stderr into what they check. Harmless in CI. Fix: clear `RUBYOPT` as #15 did. Status: issue [#25](https://github.com/MadBomber/debug_me/issues/25) (trimming `spec.files` removes the `git ls-files` call).

### 2026-11-08 second visit (no audit; triage, improver, builder)

- Issues #12, #14, #16 and #17 re-verified on develop 7e12714; all still reproduce. Findings 2, 7, 10, 11, 14, 18 and 22 still present and still need Dewayne's decision.

24. **README.md:38 says `DebugMe::debug_me(){}` works with local, instance and class variables, but it raises NoMethodError.** PR #10 fixed the same claim in docs/ only. Status: issue [#20](https://github.com/MadBomber/debug_me/issues/20), closed by Copilot PR #21 on `main` only (code fix, per Dewayne); `develop` waits for the sync (findings #30).
25. **`ruby -w` on tests/debug_me_test.rb prints 15 warnings** (unused locals at lines 115, 121, 132, 358-381 and 414; constants A, B, C redefined at 244/251). Many of those locals exist so `debug_me` has something to show, so a fix must keep them (for example by referencing them or using distinct constant names). Status: constant half fixed by 80c2441 (#56); the 17 unused-local warnings left at 733fe6d were cleared by [#62](https://github.com/MadBomber/debug_me/pull/62), **merged (low risk)** 2026-11-13 → develop fb0b2ea.
26. **`bt = caller` in lib/debug_me.rb builds the whole stack on every call even when only `bt[0]` is used.** `caller(1, 1)` for the header, plus a bounded `caller` only when `levels` or `:backtrace` needs it, would avoid that. Status: issue [#26](https://github.com/MadBomber/debug_me/issues/26) (measured 2026-11-08).
27. **The gemspec's dev dependencies are not in alphabetical order** (`logger` after `rake`), which RuboCop's `Gemspec/OrderedDependencies` flags. Status: open (trivial; could ride along with a gemspec PR).

### 2026-11-08 security audit (develop 6a9ec45; main 15bd90b compared)

- bundler-audit 0.9.3 on a fresh lock (logger 1.7.0, minitest 5.27.0, rake 13.4.2): no vulnerabilities found.
- PR #21 (Copilot, `extend self` plus block-binding discovery and an autoload filter; fixes issue #20) was merged into `main`, not `develop`. Findings 28 to 30 were checked against both branches.
- Issue #16 still reproduces on both `main` and `develop`: #21 reads `eval('self.class', block.binding)`, which is still `Class` inside a class method.
- Findings #9 and #26 measured by the improver: the gem package is 158,208 bytes and would be 11,776 with `lib/` plus README, LICENSE and CHANGELOG only. At stack depth 200, `caller` takes 75 of a call's 102 us, against 0.48 us for `caller(1, 1)`.
- Dead-code scan: 7e12714..6a9ec45 (#19) read; nothing removable. Old-Ruby-only code found: the gemspec's `# coding: utf-8` and its manual `$LOAD_PATH` push, plus the `bundler` dev dependency.

28. **`debug_me { obj }` raises SyntaxError for any object with the default `to_s`.** Every block value is converted with `to_s` and then evaluated, so `"#<Object:0x...>"` becomes a comment inside `defined?(...)`. SyntaxError is a ScriptError, so a plain `rescue => e` misses it and a debug line crashes the program. Verified on 4.0.7 to 4.1.0-preview1; same code on main. Status: PR'd ([#22](https://github.com/MadBomber/debug_me/pull/22)). Follow-up from its review: the `levels` check `block_value.none? { |v| v.to_s == "backtrace" }` still matches a non-String whose `to_s` is `backtrace` (e.g. `Pathname.new("backtrace")`), which drops the `levels` backtrace. Trivial; fold into the main/develop sync.
29. **`debug_me {}` loads every autoload constant of the caller's class.** With `cconst: true`, each constant name is evaluated, which triggers a registered `autoload`. Verified on develop. main's #21 already filters `autoload?` names. Status: open (fixed on main only; comes over with the main/develop sync).
30. **`main` and `develop` have diverged.** main has #21; develop has #8 to #19 (31 commits). A trial merge conflicts in one hunk of lib/debug_me.rb (`extend self` against `**keyword_options`; keep both). After that, one test fails: `tests/module_call_test.rb:18` (`wont_include DebugMe`), because `tests/debug_me_test.rb:7` includes DebugMe at top level in the same rake process. main has no Test workflow, so #21 merged untested. develop's CHANGELOG and docs/configuration.md say `DebugMe.debug_me` was removed or doesn't exist, which is false after #21. Status: needs Dewayne's decision (in `inbox.md`).
- S-31: security finding, details held locally. Status: fix in flight.
- S-32: security finding, details held locally. Status: open.
- S-33: security finding, details held locally. Status: needs Dewayne's decision.
- S-34: security finding, details held locally. Status: needs Dewayne's action.
- S-35: security finding, details held locally. Status: open (low).

### 2026-11-09 (resumed visit; quality audit, triage, improver, two builders)

- develop advanced to 3a4651e: PRs #22, #23, #24 merged (findings #28; issue #17; issue #14/findings #19), then #36 merged.
- **Issues closed on `main` by Copilot PRs (merged by Dewayne), not on develop:** #12 (#29), #14 (#30), #16 (#31, findings #20), #20 (#21, findings #24), #25 (#33, findings #9/#23), #26 (#34, findings #26), #27 (#35, findings #15/#16). The branches diverged further; see findings #30 (inbox, dewayne.md). bomberbot's develop fixes for the same ground (#22/#24) stand; the sync reconciles both.
- Finding #12 and #23 (env_test subprocess tests leak the parent env / merge stderr): PR'd ([#37](https://github.com/MadBomber/debug_me/pull/37), merged status pending). Repro: under a noisy `RUBYOPT`, develop's env_test gives 15 failures; the branch gives 0.
- Dead-code scan 6a9ec45..e4858c9 plus whole tree: found `WorldView#everything`, an unused test helper (only its definition and a prose comment reference it). Shipped as [#36](https://github.com/MadBomber/debug_me/pull/36) (merged 2026-11-08).

#### 2026-11-09 quality audit (develop e4858c9; first quality audit)

- Tool baseline: `flog lib/debug_me.rb` = 171.4 for the one method; `flay` = 0; `reek` = 14 warnings; `rubocop` (default cops) = 67 offenses. Suite green on 4.1.0preview1, no library deprecation warnings under `-W:deprecated`. Repo has no `.rubocop.yml`/`.reek.yml`.

31. **Two constants leak into the global `Object` namespace instead of `DebugMe`.** `DebugMeDefaultOptions` (lib/debug_me.rb:15) and `DEBUG_ME_MAX_BACKTRACE` (lib/debug_me.rb:30) are defined at top level, before `module DebugMe` opens at line 33, so `require 'debug_me'` pollutes every consumer's root namespace (`ruby -Ilib -e 'require "debug_me"; p Object.const_defined?(:DebugMeDefaultOptions)'` => true). Feeds finding #22. Fix: move both inside `module DebugMe`, possibly with a deprecated top-level alias. Status: **needs Dewayne's decision** (`DebugMeDefaultOptions` is loosely public API). In `inbox.md`. Verified.
32. **`debug_me` is a single ~82-line method over every complexity threshold** (lib/debug_me.rb:34-116; flog 171, Metrics/MethodLength/AbcSize/Cyclomatic/Perceived all over default, reek TooManyStatements ~44, FeatureEnvy). The "methods testable in isolation" taste rule is unmet. Fix: extract private helpers without changing output. Status: **needs Dewayne's decision** (taste also says no wholesale restructuring). In `inbox.md`. Verified.
33. **The repo has no lint/quality gate config.** No tracked `.rubocop.yml`/`.reek.yml`, so the "zero new offenses against the repo's full config" taste rule has nothing to run, and nothing records which default cops are intentionally off for this eval-heavy gem. Fix: add a minimal `.rubocop.yml` (disable `Security/Eval`, `Style/GlobalVars`, `Style/Documentation`, set the real baseline) plus `.reek.yml`. Status: **needs Dewayne's decision** (the baseline is his). In `inbox.md`. Verified.
34. **`self.class.send('class_variables')` dispatches a public method through `send` for no reason** (lib/debug_me.rb:79; reek ManualDispatch). `self.class.class_variables` is equivalent and clearer. Status: **open**, trivial but sits in `lib/debug_me.rb` (the main/develop merge-conflict file), so fold into the sync rather than a standalone develop PR that deepens divergence. Verified. **Fixed** by #52 (e979bdb): now `context_class.class_variables`.
35. **Style/hygiene cluster on the never-linted files** (version.rb/gemspec lack `frozen_string_literal`; `Style/MutableConstant`+`Layout/HashAlignment` ×13; `debug_me.rb` `Layout/SpaceAfterComma` ×3, `Style/CommentedKeyword` ×3, `Style/FormatStringToken` ×5). The gemspec `Gemspec/OrderedDependencies` here is existing finding #27, re-confirmed at e4858c9. Status: **open**, low value; gate behind #33 so "zero new offenses" is meaningful. Verified.

- Dependency leg: `bundle outdated` = only minitest (held by gemspec `~> 5.0`, = finding #8). Ruby 4.1 readiness clean; `pp` still a default gem (no gemspec runtime dep needed, unlike `logger`).
- Security: no new findings. The two `Security/Eval` sites and `eval(..., block.binding)` calls are the same mechanics already in S-31..S-35 / finding #28, re-confirmed at e4858c9. S-31 to S-35 unchanged, details held locally.

### 2026-11-09 adoption audit (develop 3a4651e)

- mkdocs `build --strict` clean on develop. Findings #3/#4 confirmed **fixed on develop** (api-reference.md:262 `DebugMe::DEBUG_ME_VERSION`; MAX_BACKTRACE section reads "Value: `10000`"); still wrong on `main` only (for the sync). Finding #5 verified against captured output and PR'd for the four sync-safe pages ([#38](https://github.com/MadBomber/debug_me/pull/38)). Findings #6 (README.md) and #24 (README.md:38) still present on develop. Dead-code scan: improver re-read develop 3a4651e, nothing new removable beyond #36.

36. **No `llms.txt` and no committed skill artifact for the gem.** `git ls-tree -r develop` has no `llms.txt`/`SKILL.md`, and the published gh-pages site has none either, so there is no AI-/LLM-consumer entry point beyond the README. Evidence: `git ls-tree -r develop --name-only | grep -i 'llms\|skill'` → nothing; `git ls-tree origin/gh-pages --name-only` → no `llms.txt`. Proposed fix: add a short root `llms.txt` pointing at the docs pages and the one public method. Sync-safe (new root file). Status: **needs Dewayne's decision** (new convention/content, not a defect) — already covered by the standing llms.txt notes in `inbox.md` (not re-added). Verified.
37. **RubyGems metadata omits `documentation_uri`, and `spec.homepage` uses `http://`.** The docs site returns 200 (`curl -s -o /dev/null -w '%{http_code}' https://madbomber.github.io/debug_me/` → `200`) but the RubyGems listing shows `"documentation_uri": null`, because the gemspec has no `documentation_uri` key (debug_me.gemspec:18-22), and `spec.homepage` (line 13) is `http://` while metadata `homepage_uri` is `https://`. Proposed fix: add `'documentation_uri' => 'https://madbomber.github.io/debug_me/'` to `spec.metadata` and make `spec.homepage` https. **Not sync-safe** (`debug_me.gemspec` was changed on `main` since the split) — fold into the sync. Status: **issue [#39](https://github.com/MadBomber/debug_me/issues/39)**. Verified.

### 2026-11-12 security + dependencies audit (develop 0d7fb14; resumed visit)

Status re-check at 0d7fb14: develop now contains all of main (64 ahead, 0 behind), so #30 is **resolved** (Copilot #28/#32 closed #13/#17). Confirmed fixed on develop: #2/#24, #9/#23, #16/#20, #26, #29, #37 ([#43](https://github.com/MadBomber/debug_me/pull/43)), and the test_files half of #8. Still open: #8 (minitest 6), #18, #22, #28 follow-up, #31, #34 (now covered by issue #45).

38. **Empty-block discovery reads constants lexically, not from the receiver's class; autoloaded constants are bypassed.** Status: issue [#44](https://github.com/MadBomber/debug_me/issues/44); PR'd ([#52](https://github.com/MadBomber/debug_me/pull/52)).
39. **`debug_me {}` crashes when self is a BasicObject and lists the wrong locals through a Delegator.** Status: issue [#45](https://github.com/MadBomber/debug_me/issues/45); PR'd ([#52](https://github.com/MadBomber/debug_me/pull/52)).
40. **CI uses actions/checkout@v4, which runs on the deprecated Node 20 runtime.** Status: issue [#46](https://github.com/MadBomber/debug_me/issues/46).
41. **The Ruby 3.2 floor is EOL; raise it to 3.3?** Status: needs Dewayne's decision (inbox).
- S-36: security finding, details held locally. Status: needs Dewayne's decision.
- S-37: security finding, details held locally. Status: needs Dewayne's decision.

Triage/improver extras (not audit findings):

42. **`skip1:`/`skip2:` do nothing when `header: false`,** although the docs promise blank lines. Status: PR'd ([#48](https://github.com/MadBomber/debug_me/pull/48)).
43. **Ten tests fail with `DEBUG_ME=false` again** (regression of #19). Status: merged ([#49](https://github.com/MadBomber/debug_me/pull/49), low risk).
44. **api-reference mixed up `levels:` pairs and `:backtrace` frame strings** (from our #41). Status: merged ([#47](https://github.com/MadBomber/debug_me/pull/47), low risk).
45. **Two tests in tests/debug_me_test.rb (~519-554) can never fail** (mutation-proven). Status: PR'd ([#51](https://github.com/MadBomber/debug_me/pull/51)).
46. **README "only local variables" example omits `cconst: false`,** so it also prints constants. Status: merged ([#50](https://github.com/MadBomber/debug_me/pull/50), low risk, develop 8fbbbde).
47. **Suite output is noisy:** WorldView#six writes to stdout (no `file: nil`) and constants A/B/C are redefined with warnings. Status: issue [#53](https://github.com/MadBomber/debug_me/issues/53).
48. **The `skip1:`/`skip2:` comments in DebugMeDefaultOptions are misleading.** Status: open (fold into a docs PR).
49. **The gemspec `Dir[]` file list depends on the cwd** (related to S-37). Status: open.
50. **CHANGELOG has no line for the #38/#41/#47 docs fixes, and docs/changelog.md has drifted from CHANGELOG.md.** Status: CHANGELOG half is issue [#60](https://github.com/MadBomber/debug_me/issues/60) (2026-11-13, with two more `[Unreleased]` inaccuracies); docs/changelog.md half folds into #59's decision.
51. **The `:backtrace` frame format differs on Ruby 3.2/3.3 (`` `meth' ``) vs 3.4+ (`'Class#meth'`);** the docs show only 3.4+. Status: open.
52. **mkdocs-material reaches end of life in May 2027.** Status: open (watch).


### 2026-11-12 adoption audit (develop 200c10f; second visit of the day)

Re-checks at 200c10f: #5, #6, #24 fixed on develop. Still open: #7, #46, #48, #50, #51. mkdocs `--strict` clean; README examples run as documented on Ruby 4.1.0preview1; every ruby fence parses except deliberate signature notation.

53. **The live docs site, GitHub landing README and `changelog_uri` are all stale.** `origin/main` is 87ef4b7 (66 behind develop); the deploy workflow fires only on pushes to `main` and last deployed 10d92b1 (on neither branch). The live site still shows fixed bugs (#3, #5). Fix: sync/release develop to main, or `workflow_dispatch` the deploy. Status: needs Dewayne's decision (inbox).
54. **README puts shell commands inside ruby fences** (README.md:25, :46, :52) and has the heading "Examples Usage". Status: merged ([#50](https://github.com/MadBomber/debug_me/pull/50)).
55. **README never links to the docs site.** Status: merged ([#50](https://github.com/MadBomber/debug_me/pull/50)).
56. **Contributing docs don't name a target branch; README.md:348 has a `[my-github-username]` placeholder fork URL.** The placeholder is fixed by [#50](https://github.com/MadBomber/debug_me/pull/50) (merged); whether outside PRs should target `develop` needs Dewayne's decision (inbox).
57. **gem-skill readiness verified at the deterministic stage:** `gem_skill.rb sources debug_me` uses metadata + README + CHANGELOG only (14,805 chars); docs/ never feeds the skill. Status: informational; depends on README/CHANGELOG findings.
58. **The docs deploy workflow has dead parts:** an unreachable `else` arm in the Deploy step, unused pip installs (`mkdocs-macros-plugin`, `mike`), and an unread `extra.version: 1.1.4` in mkdocs.yml:93. Status: open; deploy config is never low risk, fold into #53's decision.

Improver extras (develop 200c10f; dead-code read reached 200c10f, nothing removable in 0d7fb14..200c10f):

59. **docs/changelog.md lists invented releases** (0.1.0–0.9.0, a 1.0.0 dated 2024-01-01) and CHANGELOG.md has wrong dates for 1.0.4–1.1.2 plus no 1.1.3 section (extends #7/#50). Status: needs Dewayne's decision (which file is the source of truth; inbox).
60. **'produces no output when $DEBUG_ME is false' can't see output** (passes `file: nil`); mutation-proven. Status: PR'd ([#51](https://github.com/MadBomber/debug_me/pull/51)).
61. **Four docs pages say `debug_me {}` shows "all local variables"** (README:91, getting-started.md:93-101, usage.md:39-47, api-reference.md:108-109); at top level it also dumps constants. Status: README half merged ([#50](https://github.com/MadBomber/debug_me/pull/50)); the rest waits on #22's decision.
62. **`v.to_s == 'backtrace'` (lib/debug_me.rb:74, :90) treats any object whose to_s is "backtrace" as the backtrace marker,** unlike line 99's Symbol/String check. Status: issue [#54](https://github.com/MadBomber/debug_me/issues/54).
63. **docs/contributing.md mentions a nonexistent PR template and shows a Minitest::Test skeleton while every test is spec style.** Status: open, low value.

### 2026-11-13 quality audit (develop 733fe6d; second quality audit)

- Tools (default configs, scratch GEM_HOME, Ruby 4.1.0preview1, minitest 6.1.0): flog 208.7 total, `DebugMe#debug_me` 192.8; flay 0 in lib; reek 15 (DuplicateMethodCall `block.binding` ×4); rubocop 56 lib / 384 lib+tests. `ruby -w`: lib clean; 17 unused-local warnings in tests (#25).
- Fixed since the last quality audit: #12 (#37), #34 (#52), #47 (#56), #62 (#55), #48's README half.
- Still present: #8, #14, #18, #22, #27, #31, #32, #33 (new reason: default `Style/ModuleFunction` autocorrect would turn `extend self` into `module_function` and break the public included method), #35 (plus NumericPredicate/ZeroLengthPredicate `bt_out.size > 0` ×3), #48 (lib comments), #49, #51, #58, #63.
- The improver found no docs page contradicting #48/#52/#55; every README/docs example ran as shown; `mkdocs build --strict` clean.

64. **A `BasicObject` value crashes `debug_me` with NoMethodError.** `debug_me { bo }` raises in `backtrace_marker` (`is_a?`), `{ :bo }` and ivar discovery raise in `pretty_inspect`. Pre-existing (same at e979bdb^). Fix: `Symbol === v`, `Kernel#class` via `bind_call`, `PP.pp(v, +'')`. Status: PR'd ([#63](https://github.com/MadBomber/debug_me/pull/63), 2026-11-13). Follow-ups noticed by its builder: a BasicObject with a custom `method_missing` that calls `super` still raises, and a forwarding proxy around a String is still evaluated as a name (both as on develop).
65. **An empty-block dump shows the call stack in place of a local variable named `backtrace`.** Discovered `:backtrace` matches the marker. Pre-existing. Status: issue [#58](https://github.com/MadBomber/debug_me/issues/58).
66. **With `header: false` and nothing to print, `debug_me` still writes a blank line, so `skip1:` adds nothing** (`f.puts ""`). Low. Status: open (output change; ask first).
67. **With `header: false`, `:backtrace` drops the call-site frame** (`caller(1)[1..]`), so where `debug_me` was called appears nowhere; tests/debug_me_test.rb:130 pins the drop. Status: needs Dewayne's decision.
68. **Discovery reads deprecated constants and fires their deprecation warning, attributed to lib/debug_me.rb.** Low. Status: open (no clean API; document or ignore).
69. **A class that does `include DebugMe` dumps `DEBUG_ME_VERSION` on every `debug_me {}`** (inherited constants). Pre-existing. Also argues against moving more constants into `DebugMe` without `private_constant` (#31). Status: issue [#59](https://github.com/MadBomber/debug_me/issues/59); PR'd [#66](https://github.com/MadBomber/debug_me/pull/66) (2026-11-13 08:45).
70. **Nothing tests `time:` / `strftime:`;** forcing a wrong format or deleting the timestamp left the suite green (improver mutation). Status: PR'd ([#61](https://github.com/MadBomber/debug_me/pull/61), 2026-11-13).
- Security: no new finding; S-36 re-confirmed at 733fe6d (details held locally).

### 2026-11-13 08:45 adoption audit (develop fb0b2ea; third adoption audit)

- `mkdocs build --strict` clean; every ruby fence parses except signature notation; probes (no-include `DebugMe.debug_me {}`, Pathname label, `skip1:` with `header: false`, `DEBUG_ME_VERSION`) match the docs; metadata URIs return 200; CHANGELOG `[Unreleased]` covers e2ce9d8, e979bdb, 53b7edd.
- Still open: #7/#59 (no `[1.1.3]` section), #36 (`llms.txt`), #50/#59 (docs/changelog.md behind), #53 (main 74 behind), #58, #63.

71. **The docs never say an undefined name prints the String `"<undefined>"`** (lib/debug_me.rb:122), indistinguishable from a value that is that string. `grep -rni undefined README.md docs/*.md` → nothing. Low. Status: issue [#67](https://github.com/MadBomber/debug_me/issues/67).
72. **"backtrack" typo in the `levels:` comment** (README.md:253, lib/debug_me.rb:18). Trivial. Status: folded into issue [#67](https://github.com/MadBomber/debug_me/issues/67).
73. **CHANGELOG `[Unreleased]` doesn't mention the new documentation site** (fd375eb, after v1.1.3). Status: PR'd [#65](https://github.com/MadBomber/debug_me/pull/65).

Builder and improver extras (2026-11-13 08:45):

74. **A `logger:` without `#debug` is silently skipped,** including a proxy that answers `debug` via `method_missing` without `respond_to_missing?`; passing `$stderr` as `logger:` drops output with no warning. The guard itself is now pinned by [#64](https://github.com/MadBomber/debug_me/pull/64). Whether to warn or raise is an API choice. Status: needs Dewayne's decision (inbox).
75. **An explicit `{ :DEBUG_ME_VERSION }` in a block written at top level prints `<undefined>`** for an object that includes DebugMe (names are evaluated in the block's scope). Low. Status: open.
76. **The gemspec's `bundler` development dependency is redundant** (default gem since 2.6, floor 3.2) and the dev dependencies are unsorted (extends #27). Gemspec, so never low risk. Status: open.
- Security: no new finding; S-36 re-confirmed by a builder at fb0b2ea (details held locally).
