# Findings: debug_me

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | never | |
| dead code | 2026-11-06 | Quick scan only. lib is about 105 lines and every method and option is reachable. |
| dependencies | never | |
| adoption | 2026-11-06 | First audit. README, docs site (mkdocs builds), CHANGELOG, gemspec packaging. gem-skill and llms.txt not checked. |
| quality | never | |

Dead-code scan last covered commit: 9e0f9be (develop, 2026-11-06)

## Findings

### 2026-11-06 adoption audit

1. **A String tag followed by other options raises ArgumentError.** `debug_me('INFO', levels: 2)` appears in getting-started, configuration (x3), and api-reference. Status: PR'd ([#8](https://github.com/MadBomber/debug_me/pull/8)).
2. **The docs say `DebugMe.debug_me(...)` works without `include`, but it raises NoMethodError.** This is in docs/configuration.md "Without Including". There is no `module_function` or `extend self`. Fix the docs, or add `extend self` (which adds public API). Status: open. Fixing the docs is the obvious-yes version.
3. **api-reference names `DebugMe::VERSION`, which doesn't exist.** The real constant is `DebugMe::DEBUG_ME_VERSION`. Status: open.
4. **api-reference says `DEBUG_ME_MAX_BACKTRACE` defaults to 15.** The real value is 10000. Status: open.
5. **The api-reference "Output Format" section doesn't match real output.** Real output is `TIMESTAMP TAG Source:  file:line:in 'meth'`, then `TIMESTAMP TAG var -=> value`. Status: open (unverified on every sample).
6. **The README `log_me` Rails initializer example breaks when called with a block.** It passes the block positionally (`debug_me(options, block)`), which raises ArgumentError, and it overwrites its own `options` argument. Status: open.
7. **The CHANGELOG has gaps and wrong dates.**
   - CHANGELOG.md has no entry for 1.1.3 (released 2025-11-21; `$DEBUG_ME` keeps a pre-set value).
   - Its dates for 1.1.2 (2025-01-13) and 1.1.1 (2020-04-27) don't match RubyGems (2025-11-13 and 2023-11-02).
   - docs/changelog.md differs from CHANGELOG.md, and some of its content looks invented.

   Status: needs Dewayne's decision, because release headings are his.
8. **The gemspec doesn't match how tests are run.**
   - The dev dependency is `minitest ~> 5.0`, but the tests were updated for Minitest 6 and also pass on 6.1.
   - The `test_files` glob looks for `test/`, but the tests live in `tests/`.
   - The Rakefile has no `test` task, and there is no test CI workflow.

   Status: open. Adding a `rake test` task plus a minimal CI workflow would be a good next PR.
9. **The gem package includes the docs site.** `spec.files` is `git ls-files`, so it ships docs/ (about 2000 lines plus a 127 KB jpg) and mkdocs.yml. Status: open (size not measured).
10. **The `.rultor.yml` release config and `rubygems.yml.asc` look dead.** The release script's sed looks for `1.0.snapshot`, which is no longer in version.rb. Status: needs Dewayne's decision (remove, or keep for rultor).
11. **A `safe_debug`-style wrapper reports its own file and line in the header, not the caller's.** There is no option to skip a frame. Status: open idea. Adding something like a `caller_offset:` option needs Dewayne's decision, since it is new public API.
