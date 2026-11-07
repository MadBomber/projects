# Findings: prompt_manager

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | bundle-audit clean (1351 advisories); read every shell-out, ERB, YAML load, file read, ENV read. Findings S-1, S-2. |
| dead code | never | |
| dependencies | never | |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: 240b105 (develop, 2026-11-06). debride flagged only public API and the `PromptManager` alias; nothing removable without a deprecation cycle.

## Findings

### 2026-11-06 (security audit, first bomberbot pass)

- **S-1**: security finding, details held locally. Status: PR'd ([#19](https://github.com/MadBomber/prompt_manager/pull/19)).
- **S-2**: security-related design item, details held locally. Status: needs Dewayne's decision.
- **F-3**: no test CI. The only workflow (`deploy-github-pages.yml`) deploys docs. `test/test_helper.rb` turns on the 100% coverage gate "in CI", but nothing runs the suite on push or PR, so bomberbot PRs show "no checks". A small `test.yml` (checkout, `ruby/setup-ruby` on the gemspec floor 4.0.x, `bundle exec rake test`) would fix it. Status: open (the Ruby version choice may want a quick yes from Dewayne).
- **F-4**: the docs deploy workflow runs on pushes to `develop` as well as `main` (added on purpose in c5e7d62), so the public docs site shows unreleased changes. The bomberbot branch model says docs deploy from `main`. Status: needs Dewayne's decision.
- **F-5**: issue [#17](https://github.com/MadBomber/prompt_manager/issues/17) (removed keywords still prompted) targets the 0.x `FileSystemAdapter` / JSON parameter files, which were deleted in the 1.0 rewrite. No such code path exists in 1.2.0. Status: needs Dewayne's decision (close as obsolete; bomberbot may not comment on issues).
- **F-6**: `SimpleCov.add_filter` is deprecated in simplecov 1.3.2 and prints a deprecation warning on every test run (`test/test_helper.rb:8`). The replacement is `SimpleCov.skip`. Status: open (trivial).
- **F-7**: `insert` with a relative path in a string-parsed prompt resolves against the process cwd, while `include` raises `IncludeContextError` there. The docs only cover the absolute-path case. Status: unverified (may be intended).
