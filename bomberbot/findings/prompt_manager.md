# Findings: prompt_manager

Running backlog of audit findings. Append under a dated heading, never replace. Keep every finding's status current.

Statuses: open, unverified, PR'd (link), merged, fixed elsewhere, rejected, needs Dewayne's decision.

## Audits

| Audit | Last run | Notes |
|---|---|---|
| security | 2026-11-06 | bundle-audit clean (1351 advisories); read every shell-out, ERB, YAML load, file read, ENV read. Findings S-1, S-2. |
| dead code | never | |
| dependencies | 2026-11-07 | bundle outdated: only minitest 6.0.6 -> 6.1.0 and prism 1.9.0 -> 2.1.0 (dev/transitive, lockfile only); bundle-audit clean; suite green on 4.1.0-preview1, 4.0.9, LANG=C, and ubuntu x86_64 in CI; `ruby -w` load clean; no removed Ruby 4 APIs. Findings F-8, F-9. |
| adoption | 2026-11-07 | Every README example run against develop 9a385fb and matching; `mkdocs build --strict` clean; CHANGELOG has a section for every tag v0.5.x to v1.2.0; no `llms.txt`; gem-skill generation not run (it calls an LLM). Findings F-11 to F-13. |
| quality | never | |

Dead-code scan last covered commit: 9a385fb (develop, 2026-11-07, second run). The only change since 5e24e0c is #20 (`.github/workflows/test.yml`), so there was nothing to remove. Earlier: debride flags only public API; `PM::COMMAND_START` is documented in `docs/api/constants.md`, so it stays.

## Findings

### 2026-11-06 (security audit, first bomberbot pass)

- **S-1**: security finding, details held locally. Status: merged ([#19](https://github.com/MadBomber/prompt_manager/pull/19), 2026-11-07).
- **S-2**: security-related design item, details held locally. Status: needs Dewayne's decision.
- **F-3**: no test CI. The only workflow (`deploy-github-pages.yml`) deploys docs. `test/test_helper.rb` turns on the 100% coverage gate "in CI", but nothing runs the suite on push or PR, so bomberbot PRs show "no checks". A small `test.yml` (checkout, `ruby/setup-ruby` on the gemspec floor 4.0.x, `bundle exec rake test`) would fix it. Status: PR'd ([#20](https://github.com/MadBomber/prompt_manager/pull/20)); took the gemspec floor series, Ruby "4.0".
- **F-4**: the docs deploy workflow runs on pushes to `develop` as well as `main` (added on purpose in c5e7d62), so the public docs site shows unreleased changes. The bomberbot branch model says docs deploy from `main`. Status: needs Dewayne's decision.
- **F-5**: issue [#17](https://github.com/MadBomber/prompt_manager/issues/17) (removed keywords still prompted) targets the 0.x `FileSystemAdapter` / JSON parameter files, which were deleted in the 1.0 rewrite. No such code path exists in 1.2.0. Status: needs Dewayne's decision (close as obsolete; bomberbot may not comment on issues).
- **F-6**: `SimpleCov.add_filter` is deprecated in simplecov 1.3.2 and prints a deprecation warning on every test run (`test/test_helper.rb:8`). The replacement is `SimpleCov.skip`. Status: PR'd ([#21](https://github.com/MadBomber/prompt_manager/pull/21)); swapped to `skip '/test/'` and set `simplecov ~> 1.0` in the Gemfile.
- **F-7**: `insert` with a relative path in a string-parsed prompt resolves against the process cwd, while `include` raises `IncludeContextError` there. The docs only cover the absolute-path case. Status: unverified (may be intended).

### 2026-11-07 (dependencies audit)

- **F-8**: `minitest` 6.1.0 and `prism` 2.1.0 are out (lockfile has 6.0.6 and 1.9.0; prism is pulled in transitively). Neither is a security fix and nothing is broken, so no PR; refresh the lockfile at the next release. Status: open (low).
- **F-9**: Ruby 4 readiness: the gemspec floor is already `>= 4.0.9`, no runtime dependencies, `ruby -w` loads `pm` with no warnings, and a grep for removed APIs (SortedSet, `_id2ref`, pipe `open`, CGI, old Ractor API, `YAML.load`) is empty. The suite passes on 4.1.0-preview1. Status: no action needed.
- F-6 re-checked: still present on develop and prints the deprecation warning in the new CI log too. Status: open (trivial, good next PR).

### 2026-11-07 (adoption audit, second run)

- **F-10**: `test/test_helper.rb` arms the 100% gate on `ENV.key?('CI')`, so `CI=false` or an empty `CI` also arms it, so a single test file run there is held to the 100% gate (not reproduced; simplecov may merge earlier results). Surfaced by `/code-review` on #21; it predates that PR. Status: open (low).
- **F-11**: the README's "Shell expansion" bullets and "Security model" paragraph describe only command output (and inserted files) as inert. Since #19 (Unreleased), environment variable values are literal too. `docs/guides/shell-expansion.md`, `security.md` and `api/pm-module.md` already say so. Status: open (trivial docs PR; could wait for the release that ships #19).
- **F-12**: no `llms.txt`. The mkdocs site and README are complete enough to generate one from. Status: open (adoption; small PR).
- **F-13**: Material for MkDocs prints an end-of-life notice (May 5, 2027) and points to Zensical as its successor. Status: needs Dewayne's decision (inbox; not urgent).
- **F-14**: since #19, an env var value used inside the author's own ERB tag is still escaped, but ERB does not undo `<%%` inside Ruby code. `PM_X='a<%b'` with `<%= "$PM_X".upcase %>` renders `A<%%B`; before #19 it rendered `A<%B`. The docs warn that such expansions are unsafe but do not say the value gets changed. Verified on develop 9a385fb. Status: needs Dewayne's decision (tied to S-2's redesign; otherwise document it).
- **F-15**: an env value ending in `<` followed by the author's already-escaped `%%` gains a third `%`: `PM_LT='<'` with `$PM_LT%%= x` renders `<%%= x`, while writing `<%%= x` directly renders `<%= x`. Verified. Status: open (low edge case).
- **S-3**: security finding, details held locally. Status: open (verified).
- README examples verified: `PM.parse` (file, Symbol, Pathname, single word, string), metadata keys, `MissingParameterError` < `ArgumentError`, `$USER` / `\$` / inert `$(...)` output, `PM.register` plus a duplicate raising `DirectiveAlreadyRegisteredError`, `to_h` / `to_markdown`, `IncludeContextError`, `strict_parameters`, and config defaults. All match.
