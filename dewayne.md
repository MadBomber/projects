# Steps for Dewayne

Things only Dewayne can do, each tied to a PR or decision. Bomberbot adds entries; remove them when done.


## debug_me

- [ ] [debug_me#23](https://github.com/MadBomber/debug_me/pull/23): confirm the Ruby 3.2 floor, or ask for 3.3 (the oldest series still getting security fixes). Dropping Rubies makes the next release 1.2.0 rather than 1.1.4 under SemVer. At release, add the missing `[1.1.3]` CHANGELOG section and start the Unreleased compare link at v1.1.3. (added 2026-11-08)
- [ ] debug_me `main` and `develop` have diverged (Copilot #21 went to `main`). Merge one into the other before the next release; the resolution notes are in the inbox entry. (added 2026-11-08)
