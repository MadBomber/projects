# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.

- [ ] After [scripts#13](https://github.com/MadBomber/scripts/pull/13) merges: close Dependabot PRs #10, #11 and #12. They bump gems in the lockfile #13 deletes. #13 merged 2026-11-07, but they target `main`, which keeps the lockfile until `develop` is released, so close them after that merge. (Optional; added 2026-11-06)
