# Git

- Each project is its own repository (`<project>/wtree/_repo_`), and this
  root layer is another one: it tracks `AGENTS.md`, `README.md` and `.ai/`,
  and ignores the project directories.
- Commit your own work in the repository it belongs to, with a message that
  says what changed and why. No attribution trailers (`Co-Authored-By` and
  the like) in commits or PRs. A change that spans projects is one commit
  per repository, each naming what the other needs.
- Do not push, publish or tag unless the user asks.
- Everything else inside this workspace is allowed without asking: edits,
  builds, tests, benchmarks, and commits, branches, worktrees and merges into
  `main` in its repositories.
- Feature work that needs isolation uses a sibling worktree
  (`git worktree add ../<name> -b <name>` from `wtree/_repo_`) with its own
  build directory, `build/<name>_<version>`.
