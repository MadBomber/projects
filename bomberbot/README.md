# bomberbot state

- `taste.md`: rules learned from what Dewayne merges, rejects, and comments on.
- `runs.md`: one dated entry per repo visit, newest first.
- `findings/<repo>.md`: audit findings per repo, with status and the audits table. A finding that is verified and actionable becomes a GitHub issue labeled `bomberbot` in the repo, and its entry records the issue number; the file keeps the rest (unverified, needs Dewayne's decision, security placeholders).
- `plans/`: longer audit write-ups that a finding points to.

Each visit covers one repo. The asgard `bomberbot` task in `~/.prompts/bomberbot.loki` shuffles its `REPOS` list each run, renders `~/.prompts/bomberbot.md` with prompt_manager for one repo at a time, and runs a headless Claude Code session per repo. A visit cut off at the run budget keeps its directory and session and is resumed once on the next run that reaches the repo. The session's roles (tender, triage, auditor, improver, builder) are the agents under `~/.prompts/.claude/agents/`.
