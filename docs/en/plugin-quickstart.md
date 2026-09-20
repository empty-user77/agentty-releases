---
title: Plugin quick start
description: Build a working Agentty plugin — a panel, a button and a prompt — in a few minutes, with AI or by hand.
---

A plugin is a folder with a manifest and a program. Agentty starts the program when the plugin is first used and talks to it over stdin/stdout — JSON-RPC 2.0, one JSON object per line. Plugins can be written in any language; the Node.js SDK makes it a few lines.

## With AI

Open **Plugins** (the puzzle icon), enter a name and what the plugin should do under *Build your own*, and press **Create and Build with Claude Code**.

Agentty creates the plugin from a template — the SDK, its type definitions, the developer guide and a `CLAUDE.md` / `AGENTS.md` with instructions — and opens Claude Code in its folder. **Copy AI Prompt** gives you the same prompt to use with any other agent.

## By hand

A plugin folder looks like this:

```
~/.agentty/plugins/hello/
├── agentty-plugin.json   manifest
├── main.mjs              the plugin program
└── agentty-plugin.mjs    the SDK
```

The SDK is a single dependency-free file. Press **Developer Guide** on the Plugins page and Agentty unpacks it, with its types and guides, into `~/.agentty/plugins/.sdk/` — copy `agentty-plugin.mjs` from there next to your `main.mjs`.

### The manifest

```json
{
  "id": "hello",
  "name": "Hello",
  "version": "0.1.0",
  "main": "main.mjs",
  "permissions": ["prompt.inject"],
  "contributes": {
    "panel": { "title": "Hello", "icon": "sparkles" },
    "commands": [
      {
        "id": "hello.explain",
        "title": "Hello: Explain this folder",
        "icon": "bot",
        "paneBar": true
      }
    ]
  }
}
```

The `id` must equal the folder name. Everything else is in the [manifest reference](/docs/plugin-manifest).

### The program

```js
import { createPlugin, ui } from './agentty-plugin.mjs';

const plugin = createPlugin();

plugin
  .onPanelOpen((context) =>
    plugin.setPanel(
      ui.column([
        ui.text('Hello', 'title'),
        ui.text(context.pane ? `You are in ${context.pane.cwd}` : 'No terminal focused', 'muted'),
        ui.button('explain', 'Explain this folder', { icon: 'bot', variant: 'primary' }),
      ]),
    ),
  )
  .onEvent('explain', (_event, context) => explain(context))
  .command('hello.explain', ({ context }) => explain(context))
  .start();

function explain(context) {
  return plugin.injectPrompt({
    text: 'Give me a short tour of this project.',
    cwd: context.pane?.cwd,
    target: 'ask',
  });
}
```

Three things are happening:

1. `onPanelOpen` describes the panel as a tree. Agentty draws it natively — no web view.
2. `onEvent('explain', …)` runs when the button with that id is clicked.
3. `command('hello.explain', …)` runs the same code from the command palette or the pane-bar button.

### Load it

Open **Plugins → Refresh** (or reopen the page). The plugin shows up as installed, its panel button appears in the tab strip, and its command appears in the palette (⇧⌘P) and above agent panes.

> [!WARNING]
> Never write to stdout yourself — no `console.log`. Stdout carries the protocol, and anything that is not JSON is logged and ignored. Use `plugin.log(...)` or `console.error(...)`.

## The loop while you work

- **Restart** on the plugin card, or the ↻ in the panel header, picks up code changes.
- **Logs** on the card shows stderr, protocol errors, crashes and exit codes.
- **Link Folder for Development…** runs the plugin from your own folder — a Git checkout, for instance — instead of copying it. Uninstalling only unlinks it.
- **Edit with Claude Code** opens a workspace in the plugin folder.

## Make it do something useful

`context` tells you where the user is:

```js
plugin.onContextChange((context) => {
  plugin.log('focused pane:', context.pane?.kind, context.pane?.status);
});
```

Ask an agent to do something, and let the user decide where it lands:

```js
await plugin.injectPrompt({
  text: 'Write release notes for the commits since the last tag.',
  title: 'Release notes',
  target: 'ask',      // shows the Send to… dialog
  agent: 'claude',
  cwd: context.workspace?.cwd,
});
```

Read the conversation in a pane (needs `session.read`):

```js
const session = await plugin.getSession({ paneId: context.pane.id, maxTurns: 200 });
plugin.log(session.title, session.turns.length, 'turns');
```

> [!TIP]
> To have an agent produce a file — a summary, a report — ask it in the prompt to write to a path you choose, then watch for that file. That is how the Cosmica plugin saves session summaries.

## Next

- [Manifest reference](/docs/plugin-manifest) — every field
- [Node.js SDK](/docs/plugin-sdk) — handlers, calls and the UI builders
- [Protocol](/docs/plugin-protocol) — for plugins in other languages
- [Permissions](/docs/plugin-permissions) — what to ask for, and what not to do
- [Publishing](/docs/plugin-publishing) — share it with other people
