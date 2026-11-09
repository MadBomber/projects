# Brain conventions for agents

This repo is Dewayne's working list across his projects. Agents (bomberbot in
particular) read it before choosing work and update it after doing work.

- `priorities.md` is the source of truth for what matters now. Read it first.
- `repos/<name>.md` is one file per monitored repo: links, status, backlog, open PRs.
- `inbox.md` is for ideas and questions that need Dewayne's decision. Append, never prune.
- `dewayne.md` lists steps only Dewayne can do, each linked to a PR. Remove a step when it is verifiably done.
- `bomberbot/` is the bot's own state: `taste.md`, `runs.md`, `findings/<repo>.md`, `plans/`.
- `workspace/` and `*_project/` are the Asgard workspace toolkit and are not part of the brain. Do not edit them.
- Monitored repos use `main` (default, released) and `develop` (integration). Bot PRs target `develop`. This brain repo itself has only `main`.
- Mark a PR as **PR open** next to its item. Nothing is "complete" until Dewayne merges it.
- Commit only the files you changed, by explicit path, with a short title line under 60 characters.
- This repo is public. No secrets, tokens, personal data, or vulnerability details anywhere in it. Security findings appear in `bomberbot/findings/` only as placeholders; the details live in `notes/security/<repo>.md`. `notes/` is gitignored and must stay that way: never `git add -f` under it or remove it from `.gitignore`.
- Bomberbot may open GitHub issues labeled `bomberbot` in a monitored repo for findings it verified and could not fix in the same visit, at most three per visit, never for security findings. `bomberbot/findings/<repo>.md` links each one and keeps what did not become an issue.
