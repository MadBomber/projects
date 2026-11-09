# Taste

Short rules distilled from Dewayne's merges, rejections, and review comments. Newest at the bottom. Each rule cites the PR that taught it.

## Seeded from Dewayne's standing conventions (2026-11-05)

- Use the `debug_me` gem for debugging output, never `puts`.
- Methods, public or private, must be easy to test in isolation.
- Use the repo's `COMMITS.md` for commit message style when it exists, otherwise a concise title under 60 characters.
- Never take co-author credit in commit messages.
- Throwaway and utility scripts are written in Ruby with the standard library only.
- Three or more consecutive `puts` become one `puts <<~HEREDOC`.
- Diagrams in docs are SVG, dark theme, transparent background.

## Learned from PRs

- When the `/code-review` loop hits its 3-round cap and the reviewers are contradicting earlier rounds rather than finding defects, fix the trivial real items and open the PR with the overrun spelled out, instead of giving up. Dewayne chose this for [ragdoll#5](https://github.com/MadBomber/ragdoll/pull/5) and merged it.
- Two linked PRs that each fix half of a red `develop` suite are fine; he merged them back to back, without comment. ([ragdoll#4](https://github.com/MadBomber/ragdoll/pull/4), [ragdoll#5](https://github.com/MadBomber/ragdoll/pull/5))
- The fix-and-open rule for the review cap holds up: of the 10 PRs from the 2026-11-06 run, the 7 that went the full 3 rounds and opened with the overrun spelled out were all merged without comment. ([kbs#6](https://github.com/MadBomber/kbs/pull/6), [dev#1](https://github.com/MadBomber/dev/pull/1), [prompt_manager#19](https://github.com/MadBomber/prompt_manager/pull/19))
- Security fixes with neutral titles and bodies that describe the change, not the weakness, get merged as-is. ([prompt_manager#19](https://github.com/MadBomber/prompt_manager/pull/19), [asgard#6](https://github.com/MadBomber/asgard/pull/6), [scripts#13](https://github.com/MadBomber/scripts/pull/13))
- Test-infrastructure fixes (making a suite run without keys, a UTF-8 locale, or the right env) are welcome PRs on their own. ([aia#157](https://github.com/MadBomber/aia/pull/157), [htm#14](https://github.com/MadBomber/htm/pull/14), [kbs#6](https://github.com/MadBomber/kbs/pull/6))
- Keep optional-driver loading helpers plain: a `require` plus a rescue that returns a Boolean. #5 merged without comment after its review loop reverted round-1 "robustness" additions. ([shared_tools#5](https://github.com/MadBomber/shared_tools/pull/5))
- Lint changed files with the repo's full RuboCop config and get zero new offenses before opening. Dewayne fixed two `Layout/MultilineMethodCallIndentation` offenses in a test #160 added, after merging it, "so RuboCop's quality gate passes" (7208fbb). He also runs reek. ([aia#160](https://github.com/MadBomber/aia/pull/160))
- When the docs promise behaviour the code lacks, change the code, not the docs. Dewayne replied "Change the code not the documentation" on [debug_me#20](https://github.com/MadBomber/debug_me/issues/20), where BomberBot had proposed a README fix, and merged Copilot's `extend self` fix (#21).
- Don't keep code or CI for end-of-life Rubies. "drop support for the old Ruby versions." on [debug_me#17](https://github.com/MadBomber/debug_me/issues/17); the floor raise is [debug_me#23](https://github.com/MadBomber/debug_me/pull/23).
- An issue with a repro and a one-line proposed fix is a good way to ask before changing output. Dewayne answered "good idea BomberBot" on [debug_me#16](https://github.com/MadBomber/debug_me/issues/16).
- Dewayne merges bomberbot PRs with a merge commit, not squash or rebase, so each branch's small commits survive on `develop`; keep commits clean and self-describing. ([aia#157](https://github.com/MadBomber/aia/pull/157)–[#160](https://github.com/MadBomber/aia/pull/160))
- Lint and reek *test* files too, not just lib: he had to fix RuboCop `Layout` offenses himself in a test #160 added ([aia#160](https://github.com/MadBomber/aia/pull/160), follow-up 7208fbb), and in the same commit routed env-sensitive config tests through a save/restore helper. When a test touches an `ENV` key, save and restore it — don't just `ENV.delete` in `ensure`.
- When a repo's `main` and `develop` have diverged and both are getting fixes (here Copilot on `main`, bomberbot on `develop`), prefer develop PRs that touch files `main` hasn't changed since the split, so the work is sync-safe and doesn't deepen a divergence Dewayne must reconcile by hand. The env_test hardening ([debug_me#37](https://github.com/MadBomber/debug_me/pull/37)) was chosen over equally-valid fixes in `lib/debug_me.rb`/gemspec/docs for exactly this reason. (proposed 2026-11-09, pending Dewayne's confirm)
- A pure dead-test-code removal is an easy yes: Dewayne merged [debug_me#36](https://github.com/MadBomber/debug_me/pull/36) (drop the unused `WorldView#everything` helper) without comment, same as the fix/chore PRs #22-#24. (2026-11-09)
- A monitored repo with no `develop` branch is blocked for PRs: don't create `develop` and don't retarget to `main`. File the ready fixes as issues and put "create develop" in `dewayne.md`. First applied to typed_bus 2026-11-09 ([typed_bus#1](https://github.com/MadBomber/typed_bus/issues/1)–[#3](https://github.com/MadBomber/typed_bus/issues/3)), reinforced on agent99 and trak_flow the same day ([trak_flow#1](https://github.com/MadBomber/trak_flow/issues/1)–[#3](https://github.com/MadBomber/trak_flow/issues/3)). Three repos now; a standing pattern. (proposed 2026-11-09, pending Dewayne's confirm) **Superseded 2026-11-09:** the bomberbot prompt now says to create `develop` from `main` when it is missing; agent99 was the first repo where that happened ([agent99#9](https://github.com/MadBomber/agent99/pull/9)–[#11](https://github.com/MadBomber/agent99/pull/11)).
- A first visit's trio of plumbing PRs (test CI, a runnable RuboCop config, README examples that match the API) merged within minutes, without comment. Unblocking the tooling is a safe first-visit choice. ([simple_flow#9](https://github.com/MadBomber/simple_flow/pull/9)–[#11](https://github.com/MadBomber/simple_flow/pull/11), 2026-11-09)
- A gemspec `spec.files` allowlist is never low risk, even with a clean review: the gemspec rule is absolute, whatever the builder says. ([agent99#11](https://github.com/MadBomber/agent99/pull/11), proposed 2026-11-09, pending Dewayne's confirm)
