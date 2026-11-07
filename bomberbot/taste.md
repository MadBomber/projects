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
