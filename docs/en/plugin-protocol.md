---
title: Plugin protocol
description: The JSON-RPC wire format behind the SDK — for writing Agentty plugins in any language.
---

This page describes API version **1** of the wire format. If you are writing in JavaScript, the [Node.js SDK](/docs/plugin-sdk) wraps all of it; read the [quick start](/docs/plugin-quickstart) first either way.

## Transport

Agentty starts the plugin with the plugin folder as the working directory:

| `runtime` | Command |
|---|---|
| `node` | `node <main>` — Node.js from the login shell `PATH`, Homebrew, Volta or nvm |
| `python` | `python3 <main>` |
| `executable` | `<main>` |

Messages are [JSON-RPC 2.0](https://www.jsonrpc.org/specification) objects, **one per line**, UTF-8, on stdin (Agentty → plugin) and stdout (plugin → Agentty). Lines longer than 16 MB are rejected. Anything on stdout that is not JSON is logged and ignored; stderr goes to the plugin log.

When stdin closes or `shutdown` arrives, exit. A plugin still running 1.5 seconds after `shutdown` gets `SIGTERM`, and `SIGKILL` 1.5 seconds after that. When Agentty quits, both follow immediately.

## Agentty → plugin

| Message | Kind | `params` |
|---|---|---|
| `initialize` | request — answer it | `{ apiVersion, agentty: { version }, plugin: { id, name, version, dir, dataDir }, language, context }` |
| `command/execute` | notification | `{ command, args, context }` |
| `panel/open` · `panel/close` | notification | `{ context }` |
| `ui/event` | notification | `{ element, event, value?, item?, action?, context }` |
| `context/changed` | notification | `{ context }` |
| `url/open` | notification | `{ path, query, url, context }` |
| `shutdown` | notification | `{}` |

`initialize` is sent first, followed immediately by whatever started the plugin — a command, the panel opening, or a link. Answer `initialize` with any result, for example `{}`.

## Plugin → Agentty

Send these as requests (with an `id`) to get a result or an error, or as notifications (no `id`) when you do not care.

| Method | Permission | `params` | Result |
|---|---|---|---|
| `ui/setPanel` | | `{ tree }` | `null` |
| `ui/showPanel` | | `{}` | `null` |
| `ui/notify` | | `{ message, kind }` — `info`, `success`, `warning`, `error` | `null` |
| `ui/setBadge` | | `{ text }`, max 8 characters | `null` |
| `context/get` | | `{}` | The context |
| `host/info` | | `{}` | `{ version, apiVersion, language }` |
| `host/openUrl` | | `{ url }` — http/https | `null` |
| `host/revealPath` | `workspace.read` | `{ path }` — absolute, existing | `null` |
| `prompt/inject` | `prompt.inject` | `{ text, title?, target?, paneId?, workspaceId?, agent?, cwd?, submit? }` | `{ status: "asked" }` or `{ status: "sent", paneId }` |
| `terminal/send` | `terminal.write` | `{ paneId?, text, submit? }` — focused pane without `paneId` | `{ paneId }` |
| `session/get` | `session.read` | `{ paneId?, maxTurns? }` — default 200, max 2000 | `{ paneId, agent, sessionId, title, cwd, status, turnCount, turns }` |
| `workspace/list` | `workspace.read` | `{}` | `[{ id, name, cwd, active, panes }]` |

Agentty drops `ui/notify` calls arriving faster than one per 700 ms, answering them normally, and stops a plugin that sends more than 240 messages a second. Context fields are limited by the plugin's permissions.

### Error codes

| Code | Meaning |
|---|---|
| `-32601` | Unknown method |
| `-32602` | Invalid parameters — bad UI tree, no such pane, … |
| `-32001` | Permission missing, or blocked for a minute after a link reached the plugin |
| `-32002` | Unavailable — no window open, no session yet |

## An example exchange

```
→ {"jsonrpc":"2.0","id":1,"method":"initialize","params":{"apiVersion":1,"plugin":{"id":"hello"},"language":"en","context":{}}}
→ {"jsonrpc":"2.0","method":"panel/open","params":{"context":{}}}
← {"jsonrpc":"2.0","id":1,"result":{}}
← {"jsonrpc":"2.0","id":1,"method":"ui/setPanel","params":{"tree":{"type":"column","children":[{"type":"button","id":"go","label":"Go"}]}}}
→ {"jsonrpc":"2.0","id":1,"result":null}
→ {"jsonrpc":"2.0","method":"ui/event","params":{"element":"go","event":"click","context":{}}}
← {"jsonrpc":"2.0","id":2,"method":"prompt/inject","params":{"text":"Hello","target":"ask"}}
→ {"jsonrpc":"2.0","id":2,"result":{"status":"asked"}}
```

`→` is Agentty to plugin, `←` is plugin to Agentty. Request ids are counted per direction.

## UI tree

Every node is an object with a `type`:

```
column   { children, gap? }                gap: none | small | medium | large
row      { children, gap?, wrap? }
section  { title, children }
text     { text, style? }                  style: body | title | muted | small | code | error | success
button   { id, label, icon?, variant?, disabled? }   variant: primary | secondary | ghost | danger
input    { id, placeholder?, value? }
list     { id, items, empty? }
         items: [{ id, title, subtitle?, detail?, icon?, tone?, actions?: [{ id, label?, icon?, tooltip? }] }]
choice   { id, options: [{ value, label }], value? }
toggle   { id, label, value? }
badge    { text, tone? }                   tone: neutral | info | success | warning | error
spinner  { text? }
divider  {}
```

Events: `button` sends `click`; `input` sends `change` and `submit` with `value`; `list` sends `select` with `item`, and row buttons send `action` with `item` and `action`; `choice` sends `change` with the option value; `toggle` sends `change` with the new boolean.

A list item's `tone` colors its icon, using the same values as `badge`.

## Testing without Agentty

The plugin is an ordinary program reading stdin and writing stdout, so you can drive it from a test: write an `initialize` request, then the notifications you want to exercise, and assert on the JSON the plugin writes back.
