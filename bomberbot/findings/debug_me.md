# Findings: debug_me

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | 2026-11-08 | Quick scans through 3c1bd45; on 2026-11-08 the improver read 3c1bd45..77b8c25 (#11, #15) and the whole tree: an unused `amazing_print` test dependency and a dead Ruby 1.8.6 branch (findings #21, issue #17). A second 2026-11-08 read of 77b8c25..7e12714 (#18) found nothing removable. |
| dependencies | never | |
| adoption | 2026-11-06 | First audit. README, docs site (mkdocs builds), CHANGELOG, gemspec packaging. gem-skill and llms.txt not checked. |
| quality | never | |

Dead-code scan last covered commit: 7e12714 (develop, 2026-11-08)

## Findings

### 2026-11-06 adoption audit

1. **A String tag followed by other options raises ArgumentError.** `debug_me('INFO', levels: 2)` appears in getting-started, configuration (x3), and api-reference. Status: merged ([#8](https://github.com/MadBomber/debug_me/pull/8), 2026-11-07).
2. **The docs say `DebugMe.debug_me(...)` works without `include`, but it raises NoMethodError.** This is in docs/configuration.md "Without Including". There is no `module_function` or `extend self`. Fix the docs, or add `extend self` (which adds public API). Status: PR'd ([#10](https://github.com/MadBomber/debug_me/pull/10), docs fix). Making `DebugMe.debug_me` work via `extend self` still needs Dewayne's decision.
3. **api-reference names `DebugMe::VERSION`, which doesn't exist.** The real constant is `DebugMe::DEBUG_ME_VERSION`. Status: PR'd ([#10](https://github.com/MadBomber/debug_me/pull/10)).
4. **api-reference says `DEBUG_ME_MAX_BACKTRACE` defaults to 15.** The real value is 10000. Status: PR'd ([#10](https://github.com/MadBomber/debug_me/pull/10)).
5. **The api-reference "Output Format" section doesn't match real output.** Real output is `TIMESTAMP TAG Source:  file:line:in 'meth'`, then `TIMESTAMP TAG var -=> value`. Status: open (unverified on every sample).
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
9. **The gem package includes the docs site.** `spec.files` is `git ls-files`, so it ships docs/ (about 2000 lines plus a 127 KB jpg) and mkdocs.yml. Status: open (size not measured).
10. **The `.rultor.yml` release config and `rubygems.yml.asc` look dead.** The release script's sed looks for `1.0.snapshot`, which is no longer in version.rb. Status: needs Dewayne's decision (remove, or keep for rultor).
11. **A `safe_debug`-style wrapper reports its own file and line in the header, not the caller's.** There is no option to skip a frame. Status: open idea. Adding something like a `caller_offset:` option needs Dewayne's decision, since it is new public API.

### 2026-11-07 re-check (no audit; 5 verified open findings)

- Findings 2, 3, 4 and 6 confirmed still present on develop 3055a9f. Each is a small docs fix and a good next PR.

12. **`tests/env_test.rb` restores `ENV["DEBUG_ME"]` only if its assertion passes (no `ensure`), and shells out to a bare `ruby` from PATH instead of `RbConfig.ruby`.** Raised by `/code-review` on #9. Status: open (low value).

### 2026-11-07 second run (no audit; found by `/code-review` on #10)

- Findings 5 and 6 confirmed still present on develop 98b8d47. Finding 5's "With Backtrace" sample is wrong too: real output is `TAG backtrace -=> [[0, "file:line:in ..."], ...]`, not `caller(0) -=>` with one frame per line.

13. **`debug_me(levels: 1) { :backtrace }` prints the backtrace twice.** With `levels > 0`, lib/debug_me.rb adds `'backtrace'` to the output list itself, then evaluates the block's own `:backtrace` again. Confirmed at runtime on develop 98b8d47. README.md says `levels` replaces `:backtrace`. Status: PR'd ([#11](https://github.com/MadBomber/debug_me/pull/11)).
14. **`levels:` is silently ignored with `header: false`, and a String `levels:` raises.** The `levels` backtrace is collected only inside `if options[:header]`, and `options[:levels] > 0` runs before `.to_i`, so `debug_me(levels: '2')` raises (ArgumentError on Ruby 4, not NoMethodError). Status: String half is issue [#13](https://github.com/MadBomber/debug_me/issues/13), merged ([#18](https://github.com/MadBomber/debug_me/pull/18), 2026-11-08); whether `levels:` should work with `header: false` needs Dewayne's decision.
15. **configuration.md's "Include at Kernel Level" heading and its "includes into Object/Kernel" comment are wrong.** A top-level `include` adds the module to Object, not Kernel. Status: open (docs; the heading anchor may be linked).
16. **api-reference's signature line still reads `debug_me(options = {}, &block)` and leaves out `**keyword_options`** (added in #8). Status: open (docs).
17. **On Ruby 2.7, passing a Hash variable to `debug_me` may print the "last argument as keyword parameters" deprecation warning** since #8 added `**keyword_options`. Status: unverified (needs a Ruby 2.7 run; CI covers 2.3 and 4.0 only).

### 2026-11-07 third run (no audit; found by `/code-review` on #11)

- Findings 5, 6, 12, 14, 15 and 16 still open on develop 3c1bd45 (not re-verified one by one; the #10 merge touched only docs, and none of these were in its scope).

18. **With `levels > 0`, an empty block or one returning `nil` no longer dumps local, instance and class variables.** `levels` puts `backtrace` in the list before the `block_value.empty?` fallback runs, so `debug_me(levels: 2) { }` shows only the backtrace, while `debug_me { }` dumps the variables. Status: open needs Dewayne's decision (is the variable dump wanted alongside `levels`?). Re-confirmed on f7041aa.

### 2026-11-08 (no audit; triage and improver)

- Issues #12, #13 and #14 re-verified on develop 77b8c25. #12 and #14 still reproduce.
- The 2026-11-07 late visit (cut off, no run entry) opened issues #12, #13 and #14 and PR #15 (merged 2026-11-08). Finding 19 records #14, which had no finding.

19. **The test suite fails with `DEBUG_ME=false` in the shell and leaves `logger_class_output.txt` behind, which keeps later runs red.** Status: issue [#14](https://github.com/MadBomber/debug_me/issues/14).
20. **`debug_me {}` in a class method, module method or class body shows no class variables or constants.** `cvar`/`cconst` read `self.class`, which is `Class` there. Verified on 77b8c25. Status: issue [#16](https://github.com/MadBomber/debug_me/issues/16).
21. **The test suite requires `amazing_print` (and the gemspec lists it) but nothing uses it; `lib/debug_me.rb` keeps a Ruby 1.8.6 `caller` branch that can't run on Ruby >= 2.3.** Verified on 77b8c25: the suite counts are unchanged without it. Status: issue [#17](https://github.com/MadBomber/debug_me/issues/17), PR'd ([#19](https://github.com/MadBomber/debug_me/pull/19)).
22. **A top-level `debug_me {}` dumps every top-level constant** (`self.class` is Object; 429 lines in one probe for one local). Status: needs Dewayne's decision (skip constants for Object, default `cconst` off at top level, or document it). In `inbox.md`.
23. **The 11 subprocess tests in `tests/env_test.rb` fail outside a git checkout under `bundle exec`.** They inherit `-rbundler/setup`, the gemspec's `git ls-files` writes `fatal: not a git repository` to stderr, and the tests merge stderr into what they check. Harmless in CI. Fix: clear `RUBYOPT` as #15 did. Status: open (fold into a fix for #14 or findings #12).

### 2026-11-08 second visit (no audit; triage, improver, builder)

- Issues #12, #14, #16 and #17 re-verified on develop 7e12714; all still reproduce. Findings 2, 7, 10, 11, 14, 18 and 22 still present and still need Dewayne's decision.

24. **README.md:38 says `DebugMe::debug_me(){}` works with local, instance and class variables, but it raises NoMethodError.** PR #10 fixed the same claim in docs/ only. Status: issue [#20](https://github.com/MadBomber/debug_me/issues/20).
25. **`ruby -w` on tests/debug_me_test.rb prints 15 warnings** (unused locals at lines 115, 121, 132, 358-381 and 414; constants A, B, C redefined at 244/251). Many of those locals exist so `debug_me` has something to show, so a fix must keep them (for example by referencing them or using distinct constant names). Status: open (low value).
26. **`bt = caller` in lib/debug_me.rb builds the whole stack on every call even when only `bt[0]` is used.** `caller(1, 1)` for the header, plus a bounded `caller` only when `levels` or `:backtrace` needs it, would avoid that. Status: unverified (not measured).
27. **The gemspec's dev dependencies are not in alphabetical order** (`logger` after `rake`), which RuboCop's `Gemspec/OrderedDependencies` flags. Status: open (trivial; could ride along with a gemspec PR).
