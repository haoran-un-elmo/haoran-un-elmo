# Session Naming

- At session start, check if inside a git worktree (`git worktree list`).
  If yes, and no session name exists, use the `/rename` command to set the 
  session name to the worktree directory name.
- After 3 prompts, if no session name has been set, summarise the
  conversation focus in 3-5 words and set that as the session name.
- Use `<your mechanism here>` to set the session name.
