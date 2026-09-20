---
title: Git and working trees
description: The git page, branch switching from any pane, and the worktree per session that keeps agents out of each other's way.
---

## The git page

**⇧⌘G** opens a page in the style of GitHub Desktop:

- Switch repository and branch.
- **Fetch** (⇧⌘T), **pull**, **push** (⌘P), **refresh** (⌘R).
- Review the diff file by file, stage what you want, and commit (⌘↩).
- Browse history, and merge branches.

Agentty drives your own `git`, with your configuration and credentials. Every command runs non-interactively, so nothing can hang waiting for a prompt you can't see — if a push needs credentials your credential helper does not have, it fails with a message instead of stalling.

## From a pane

Click the branch name in any pane header to switch branches, pull, push, or copy the name — without leaving your terminal.

## A worktree per session

When you open a second AI session in a project where another one is already working, Agentty starts it in **its own git worktree on a new branch**, taken from the project's default branch. The two agents never edit the same files.

- Worktrees live in `~/.agentty/worktrees/`, on branches named `agentty/<name>`.
- Turn this off in **Settings → General** if you want every session in the same folder.

### How it works

Agentty runs `git worktree add` on a new branch `agentty/<name>`, taken from the project's **default branch** rather than whatever the project folder currently has checked out — so a session never builds on another session's unmerged work.

The trees are created under `~/.agentty/worktrees/<project>-<hash of its path>/<name>/`, outside the project. Keeping them out of the project folder matters: a copy of the code inside it would be walked by every search, file watcher and build.

Agentty only ever removes trees under that folder. Worktrees you made yourself are listed in the files panel and left alone.

## The files panel

**⌥⌘B** docks the project tree beside your terminals. It also lists **every working tree** of the project, who is working in each one, and what changed there.

Right-click a tree to open a terminal in it, copy its path, or remove it (and its branch, once merged).

## Parallel tasks

Ask an agent to split work up — "do A, B and C in parallel". It asks Agentty, you confirm once, and each task starts in its own split pane and its own worktree.

You confirm every such request; an agent cannot start panes on its own.

## The file editor

Click a file in the files panel to open it next to your terminals: syntax colors for the common languages, undo and redo, **⌘S** to save, **⇧⌥F** to format with the formatter you have installed (Prettier, google-java-format, ktlint, rustfmt, gofmt, Black).

Files an agent changes reload by themselves. **Open in editor** hands the file to VS Code, Cursor or your system editor. Files are only ever read and written, never run.
