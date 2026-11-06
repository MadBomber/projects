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

- (none yet)
