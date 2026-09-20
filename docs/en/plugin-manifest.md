---
title: Manifest reference
description: Every field of agentty-plugin.json — identity, runtime, permissions, panel, commands and icons.
---

`agentty-plugin.json` sits at the root of the plugin folder and tells Agentty what the plugin is, how to start it, what it may do, and what it adds to the interface.

```json
{
  "id": "hello",
  "name": "Hello",
  "version": "0.1.0",
  "main": "main.mjs",
  "runtime": "node",
  "apiVersion": 1,
  "description": "Turns the current folder into a prompt.",
  "publisher": "you",
  "homepage": "https://example.com/hello",
  "permissions": ["prompt.inject"],
  "contributes": {
    "panel": { "title": "Hello", "icon": "sparkles" },
    "commands": [
      { "id": "hello.explain", "title": "Hello: Explain this folder", "icon": "bot", "paneBar": true }
    ]
  }
}
```

## Identity

| Field | Required | Notes |
|---|---|---|
| `id` | yes | 2–40 characters of `a-z`, `0-9` and `-`. **Must equal the folder name.** |
| `name` | yes | Shown in the store and on the panel button |
| `version` | yes | `major.minor.patch` |
| `description` | | One line for the store card |
| `publisher` | | Who made it |
| `homepage` | | Must be `https://` |
| `keywords` | | For search in the store |
| `links` | | Up to 6 `{ "label", "url" }` (https) shown as buttons on the card — project site, docs, source |
| `icon` | | An icon name from the list below |

## Running

| Field | Default | Notes |
|---|---|---|
| `main` | required | Entry point, relative to the plugin folder |
| `runtime` | `node` | `node` (Node.js 18+ from the login shell `PATH`), `python` (`python3 <main>`), or `executable` (`<main>` is run directly) |
| `apiVersion` | `1` | The plugin API version the plugin was written for |
| `activationEvents` | `[]` | `["onStartup"]` starts the plugin with Agentty; otherwise it starts on first use |

Agentty starts the program with the plugin folder as its working directory.

## Integration with another app

| Field | Notes |
|---|---|
| `requires` | `{ "name", "url", "note" }` — the app or service this plugin is for. The store card says whether it was found and offers the link when it wasn't |
| `detect` | Paths (`~` allowed) of the app the plugin integrates with. Found → the card is marked **Recommended** |

## Permissions

```json
"permissions": ["prompt.inject", "terminal.write", "session.read", "workspace.read"]
```

| Permission | Allows |
|---|---|
| `prompt.inject` | Sending prompts |
| `terminal.write` | Typing into open panes |
| `session.read` | Reading AI conversations |
| `workspace.read` | Listing workspaces, and seeing folder and title fields in the context |

Ask only for what you use — the list is shown to the user before installation. A call without its permission fails. See [Plugin permissions](/docs/plugin-permissions).

## What the plugin contributes

### Panel

```json
"contributes": { "panel": { "title": "Hello", "icon": "sparkles" } }
```

Gives the plugin a button in the tab strip and a panel docked right of the terminals (360 px wide, scrolls vertically). The plugin fills it with a UI tree — see the [SDK](/docs/plugin-sdk).

### Commands

```json
"contributes": {
  "commands": [
    {
      "id": "hello.explain",
      "title": "Hello: Explain this folder",
      "description": "Sends a tour request to the focused agent",
      "icon": "bot",
      "paneBar": true,
      "when": "agent",
      "palette": true
    }
  ]
}
```

| Field | Notes |
|---|---|
| `id` | Unique within the plugin; the SDK registers handlers by this id |
| `title` | Shown in the palette; prefix it with the plugin name so it groups well |
| `description` | Optional second line |
| `icon` | Icon name for the pane-bar button |
| `paneBar` | `true` adds an icon button to the status bar above Claude Code / Codex panes and to split-pane headers |
| `when` | Limits the pane-bar button to `agent` panes, `shell` panes, or `always` |
| `palette` | `false` hides it from the command palette |

A pane-bar command receives the context of the pane whose button was pressed, not the focused pane.

## Icons

Use these names for any `icon` field. Anything else shows a puzzle piece.

```
app-window arrow-down arrow-left arrow-right arrow-up arrow-up-right at-sign bell bell-dot
blocks book-open bookmark bot brain bug calendar chart-column check chevron-down chevron-right
chevron-up circle-check circle-dot circle-pause circle-x clipboard clipboard-paste clock cloud
code columns-2 command container copy database download ellipsis external-link eye file-input
file-plus file-text folder folder-open folder-plus git-branch git-commit-horizontal
git-pull-request globe grip-vertical hammer hash history house image info key-round
layout-panel-left lightbulb link list list-tree loader-circle mail maximize-2 message-circle-question
message-square minimize-2 minus network notebook notebook-pen package panel-left-close
panel-left-open pencil picture-in-picture-2 play plug plus power puzzle refresh-cw rocket rotate-cw
rows-2 save scroll-text search send settings shield-alert sparkles square square-plus
square-terminal star sticky-note tag terminal trash-2 undo-2 unlink upload users wand-sparkles
workflow wrench x zap git-fork file lock graduation-cap
```

## Environment

The plugin program gets these environment variables:

| Variable | Meaning |
|---|---|
| `AGENTTY_PLUGIN_ID` | The plugin's id |
| `AGENTTY_PLUGIN_DIR` | The plugin folder |
| `AGENTTY_PLUGIN_DATA` | A private folder for settings and caches |
| `AGENTTY_VERSION` | The Agentty version |
| `AGENTTY_LANGUAGE` | The user's language (`en`, `ko`, `ja`, `zh`) |
| `AGENTTY_BIN` | Path to the `agentty` command line helper |

Keep everything you persist inside `AGENTTY_PLUGIN_DATA`.
