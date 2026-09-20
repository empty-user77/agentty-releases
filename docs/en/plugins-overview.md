---
title: Plugins
description: What plugins add to Agentty, how to install and use them, and how links from other apps hand work to your terminals.
---

Plugins connect Agentty with other apps and add tools to your terminals. A plugin can put a **panel** next to your terminals, add **buttons** above agent panes, add entries to the **command palette**, and hand text to an agent as a **prompt** — always after you choose where it goes.

Writing one? Start with the [plugin quick start](/docs/plugin-quickstart).

## The Plugins page

Open it from the activity bar (the puzzle icon), **View → Plugins**, or the command palette (⇧⌘P → "Plugins").

- **Installed** — every plugin you have, with what it adds and what it may do.
- **Available** — plugins that ship with Agentty. If the app a plugin integrates with is installed, it is marked **Recommended**.
- **Build your own** — create a plugin with AI, or install one from a Git repository or a folder.

Each installed plugin has **Enable / Disable**, **Open Panel**, **Restart** (after editing its code), **Update** (when Agentty ships a newer built-in version), **Logs** (what the plugin printed, crashes included), **Show in Finder**, and **Uninstall** (press twice to confirm).

Plugins start when you first use them, not when Agentty launches, so an idle plugin costs nothing.

## Installing

| From | How |
|---|---|
| Built in | **Install** on a card under *Available* |
| A Git repository | Paste an `https://` URL under *Build your own* → **Install from Git** |
| A folder | **Install from Folder…** copies it into `~/.agentty/plugins/` |
| A folder you are editing | **Link Folder for Development…** runs it where it is; uninstalling only unlinks it |

You can also copy a plugin folder into `~/.agentty/plugins/` yourself and press **Refresh**.

> [!IMPORTANT]
> Before installing, the card lists what the plugin may do: send prompts, type into terminals, read AI conversations, see workspaces. A plugin runs as a normal program with your user's access, so install only plugins you trust. The model is explained in [Plugin permissions](/docs/plugin-permissions).

## Using a plugin

- **Panel** — plugins with a panel get a button in the tab strip. Click it to open the panel next to your terminals, click again to close. The ↻ in the panel header restarts the plugin; the gear opens the Plugins page.
- **Buttons above terminals** — plugin commands can appear as icons in the status bar above a Claude Code or Codex pane, and in split-pane headers. They act on that pane.
- **Command palette** — ⇧⌘P lists every plugin command under *Plugin*.

### The "Send to…" dialog

When a plugin, or a link from another app, sends a prompt, Agentty shows what will be sent and lets you choose:

- **Claude Code / Codex / Terminal**
- **New workspace** (with a folder you can change), **New tab** in the current workspace, or one of your **open workspaces** — an idle agent there receives it, otherwise a new agent tab opens
- **Send right away**, or leave it unchecked to have the text typed in without pressing Enter

Terminals only ever get the text typed in; Agentty never runs it for you. Nothing is sent until you press **Send**.

## Links from other apps

Other apps can hand work to Agentty with `agentty://` links — for example Cosmica's "Continue in Agentty".

| Link | Effect |
|---|---|
| `agentty://plugin/<id>/<path>?key=value` | Starts plugin `<id>` and calls its URL handler |
| `agentty://prompt?text=…&title=…&agent=…&cwd=…` | Opens **Send to…** with the text (`file=` attaches an absolute `.md` / `.txt` path) |
| `agentty://plugins/<id>` | Opens the Plugins page at that plugin |

If a link needs a built-in plugin you don't have yet, Agentty offers to install it and then continues with the link. Prompts that arrive this way always go through **Send to…**.

> [!NOTE]
> Links work with the installed application. A build started from a development shell is not registered with the operating system as the handler for `agentty://`.

## Cosmica

[Cosmica](https://www.cosmica.ink/) is a notes app. Its plugin ships with Agentty and is marked *Recommended* when Cosmica is installed. It reads only the notes folder and local port from Cosmica's own settings.

1. **Plugins → Install** on the Cosmica card, then open its panel from the tab strip.
2. **Notes → prompt** — search your notes, then insert one into the focused terminal or continue it somewhere you choose.
3. **From Cosmica** — right-click a note → **Continue in Agentty**. The note is saved first, then **Send to…** opens in Agentty.
4. **Session → Cosmica** — with a Claude Code or Codex pane focused, **Save AI summary** asks the agent to write a structured summary (goal, what was done, files changed, decisions, next steps) into `Agentty/` in your notes. **Save conversation log** stores the raw conversation without asking the AI anything.

Busy agents are left alone; try again when the turn finishes. While Cosmica is running, notes are saved through its local API so they are indexed right away; otherwise the file is written and Cosmica picks it up on its next start.

## If something goes wrong

| Symptom | What to do |
|---|---|
| "Node.js was not found" | Plugins written in JavaScript need Node.js 18+ on your login shell's `PATH` |
| A card says the plugin stopped with an error | Open **Logs** on the card; after fixing the code press **Restart** |
| A plugin you copied in doesn't appear | Press **Refresh**, and check that the folder name matches the `id` in `agentty-plugin.json` |
| A link does nothing | Use the installed application, and make sure the plugin the link names is installed and enabled |
| A plugin behaves oddly after an update | **Restart** it, or **Disable** it and report the problem to its author |

## Where things are

| Path | Purpose |
|---|---|
| `~/.agentty/plugins/<id>/` | The plugin |
| `~/.agentty/plugins/state.json` | Which plugins are enabled, and where development links point |
| `~/.agentty/plugin-data/<id>/` | A plugin's own settings and caches |
| `~/.agentty/plugins/.sdk/` | The SDK and guides, unpacked by **Developer Guide** |
| `~/.agentty/prompts/` | Long prompts handed to agents as files |

Uninstalling removes the plugin; its data folder stays until you delete it.

## Next

- [Plugin quick start](/docs/plugin-quickstart) — write one in a few minutes
- [Manifest reference](/docs/plugin-manifest) · [Node.js SDK](/docs/plugin-sdk) · [Protocol](/docs/plugin-protocol)
- [Permissions](/docs/plugin-permissions) · [Publishing](/docs/plugin-publishing)
