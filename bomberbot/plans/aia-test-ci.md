# aia: test CI workflow (blocked on token scope)

Built by bomberbot on the 2026-11-13 00:15 visit (branch `bomberbot/test-ci`, commit 0426821, never pushed). GitHub refused the push: the bots' `gh` token lacks the `workflow` scope. aia has no test CI today; only the docs deploy workflow runs. Local proof: `bundle exec rake test` with 2001 tests, 0 failures, in a clean env. `/code-review` was clean in 2 rounds.

To land it: either `gh auth refresh -h github.com -s workflow` (then the next visit rebuilds and pushes it) or add this file yourself as `.github/workflows/test.yml` on a branch off develop, plus a CHANGELOG `[Unreleased]` `### Added` line.

```yaml
name: Test
on:
  push:
    branches:
      - main
      - develop
  pull_request:

permissions:
  contents: read

jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 15
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup Ruby
        uses: ruby/setup-ruby@v1
        with:
          ruby-version: "4.0"
          bundler-cache: true

      - name: Run tests
        run: bundle exec rake test
```

Note: `test/aia/fzf_test.rb` skips when fzf is missing, so on ubuntu it skips silently; add `sudo apt-get install -y fzf` if you want it to run.
