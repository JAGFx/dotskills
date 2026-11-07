---
name: study
description: Study mode, invoked by the user only (/study). Explore, read and analyze a subject without moving the dev in progress, no change to the repo, its git state, its databases or its generated files. A change, when needed, only in a worktree created from a branch the user names.
disable-model-invocation: true
---

# Study

The user works on a dev in parallel. This mode lets Claude explore a subject without moving anything of that dev: its working tree, its index, its branches, its databases, its generated files. It lasts until the user explicitly ends it (« on sort », « tu peux modifier »).

## Allowed

- Read and search the whole repo. Exploring is the point here: it overrides any « no exploration » rule of the project's instructions.
- Git, read only: `status`, `log`, `diff`, `show`, `blame`, `grep`; another branch through `git show <ref>:<path>`, never a checkout. `git fetch`: it touches neither the working tree nor the local branches.
- Read-only SQL (`SELECT`) on the local database.
- Temporary files in the scratchpad, outside the repo.
- Web searches, docs.

## Not allowed in the main checkout

- Creating, editing or deleting any file of the repo, tracked or not, docs and dev journal included.
- Git commands that change the working tree, the index or the branches: `checkout`, `switch`, `stash`, `reset`, `commit`, `pull`, `rebase`, `merge`.
- Commands that write or regenerate: migrations, seeds, writing commands, tests (even on a test database, they may collide with the user's runs), formatters, ide-helper, lint, type generation, dependency installs, service restarts.
- Writing to memory, unless asked.

## Changing code: a worktree

When trying something needs a change, propose a worktree and ask from which branch to create it before creating it. In the worktree, edits and commands are allowed as long as the main checkout and its shared services stay untouched (follow the project's procedure for running in a worktree).

Closing the worktree:

- « commit » or « clôture » at the end of the dev: commit the worktree's changes on its branch (project's commit conventions), then remove the worktree and what was set up to run it; the branch stays, with the commit. Give the branch and the commit hash.
- Mode ending otherwise: ask whether to keep or remove the worktree.

## Results

Findings stay in the conversation; a file only when asked, in the scratchpad or in the worktree. A request that would change the main checkout: describe the change, do not apply it.
