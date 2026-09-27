# Parallel Work & Worktrees & Branch Discipline

Before code work in a git repo, check for parallel or existing work.

1. Don't commit or push to `master`/`main` unless explicitly instructed otherwise.
2. Check for worktrees (`git worktree list`). State branch and worktree path you are in.
3. Check for concurrent sessions: `ListAgents`
4. If another session or worktree is active on this repo, say so and confirm the target worktree with the user before editing anything.

- Feature work belongs in a dedicated worktree.
- When beginning a new worktree, initialise it correctly (e.g. `bun install` and `bun prepare`, but check in the project's CLAUDE.md for correct method)
- Before pushing worktree work, check if we are in sync with the most recent `origin/master`
- Prefer rebase over merge when integrating with master; do not run conflict-heavy direct merges without asking
