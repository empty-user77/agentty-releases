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

## From a pane

Click the branch name in any pane header to switch branches, pull, push, or copy the name — without leaving your terminal.

## A worktree per session

When you open a second AI session in a project where another one is already working, Agentty starts it in **its own git worktree on a new branch**, taken from the project's default branch. The two agents never edit the same files.

- Worktrees live in `~/.agentty/worktrees/`, on branches named `agentty/<name>`.
- Turn this off in **Settings → General** if you want every session in the same folder.

## The files panel

**⌥⌘B** docks the project tree beside your terminals. It also lists **every working tree** of the project, who is working in each one, and what changed there.

Right-click a tree to open a terminal in it, copy its path, or remove it (and its branch, once merged).

## Parallel tasks

Ask an agent to split work up — "do A, B and C in parallel". It asks Agentty, you confirm once, and each task starts in its own split pane and its own worktree.

You confirm every such request; an agent cannot start panes on its own.

## The file editor

Click a file in the files panel to open it next to your terminals: syntax colors for the common languages, undo and redo, **⌘S** to save, **⇧⌥F** to format with the formatter you have installed (Prettier, google-java-format, ktlint, rustfmt, gofmt, Black).

Files an agent changes reload by themselves. **Open in editor** hands the file to VS Code, Cursor or your system editor. Files are only ever read and written, never run.
