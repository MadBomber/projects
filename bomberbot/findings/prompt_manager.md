# Findings: prompt_manager

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | bundle-audit clean (1351 advisories); read every shell-out, ERB, YAML load, file read, ENV read. Findings S-1, S-2. |
| dead code | never | |
| dependencies | 2026-11-07 | bundle outdated: only minitest 6.0.6 -> 6.1.0 and prism 1.9.0 -> 2.1.0 (dev/transitive, lockfile only); bundle-audit clean; suite green on 4.1.0-preview1, 4.0.9, LANG=C, and ubuntu x86_64 in CI; `ruby -w` load clean; no removed Ruby 4 APIs. Findings F-8, F-9. |
| adoption | never | |
| quality | never | |

Dead-code scan last covered commit: 5e24e0c (develop, 2026-11-07). The only change since 240b105 is #19 (`ShellExpander#append` / `#erb_escape`, both called). debride again flags only public API; `PM::COMMAND_START` is unused in lib but documented in `docs/api/constants.md`, so it is public API and stays.

## Findings

### 2026-11-06 (security audit, first bomberbot pass)

- **S-1**: security finding, details held locally. Status: merged ([#19](https://github.com/MadBomber/prompt_manager/pull/19), 2026-11-07).
- **S-2**: security-related design item, details held locally. Status: needs Dewayne's decision.
- **F-3**: no test CI. The only workflow (`deploy-github-pages.yml`) deploys docs. `test/test_helper.rb` turns on the 100% coverage gate "in CI", but nothing runs the suite on push or PR, so bomberbot PRs show "no checks". A small `test.yml` (checkout, `ruby/setup-ruby` on the gemspec floor 4.0.x, `bundle exec rake test`) would fix it. Status: PR'd ([#20](https://github.com/MadBomber/prompt_manager/pull/20)); took the gemspec floor series, Ruby "4.0".
- **F-4**: the docs deploy workflow runs on pushes to `develop` as well as `main` (added on purpose in c5e7d62), so the public docs site shows unreleased changes. The bomberbot branch model says docs deploy from `main`. Status: needs Dewayne's decision.
- **F-5**: issue [#17](https://github.com/MadBomber/prompt_manager/issues/17) (removed keywords still prompted) targets the 0.x `FileSystemAdapter` / JSON parameter files, which were deleted in the 1.0 rewrite. No such code path exists in 1.2.0. Status: needs Dewayne's decision (close as obsolete; bomberbot may not comment on issues).
- **F-6**: `SimpleCov.add_filter` is deprecated in simplecov 1.3.2 and prints a deprecation warning on every test run (`test/test_helper.rb:8`). The replacement is `SimpleCov.skip`. Status: open (trivial).
- **F-7**: `insert` with a relative path in a string-parsed prompt resolves against the process cwd, while `include` raises `IncludeContextError` there. The docs only cover the absolute-path case. Status: unverified (may be intended).

### 2026-11-07 (dependencies audit)

- **F-8**: `minitest` 6.1.0 and `prism` 2.1.0 are out (lockfile has 6.0.6 and 1.9.0; prism is pulled in transitively). Neither is a security fix and nothing is broken, so no PR; refresh the lockfile at the next release. Status: open (low).
- **F-9**: Ruby 4 readiness: the gemspec floor is already `>= 4.0.9`, no runtime dependencies, `ruby -w` loads `pm` with no warnings, and a grep for removed APIs (SortedSet, `_id2ref`, pipe `open`, CGI, old Ractor API, `YAML.load`) is empty. The suite passes on 4.1.0-preview1. Status: no action needed.
- F-6 re-checked: still present on develop and prints the deprecation warning in the new CI log too. Status: open (trivial, good next PR).
