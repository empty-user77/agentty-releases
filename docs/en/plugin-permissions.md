---
title: Plugin permissions
description: What a plugin may do, how Agentty limits it, and the rules for writing one that deserves the access it asks for.
---

A plugin runs as a normal program with your user's file and network access. Agentty does not sandbox it. What Agentty does instead is make the interesting capabilities — prompting, typing, reading conversations — explicit, declared in advance, and visible before you install.

## The four permissions

| Permission | Allows | Why it matters |
|---|---|---|
| `prompt.inject` | Sending prompts to agents | The prompt is normally shown in **Send to…** before anything happens |
| `terminal.write` | Typing into open panes | Text can reach a pane without a dialog |
| `session.read` | Reading AI conversations | Transcripts contain whatever you discussed |
| `workspace.read` | Listing workspaces, and seeing folder and title fields | Reveals where you work |

A call made without its permission fails with error `-32001`. The Plugins page lists the declared permissions on the card before you install, and again on the installed card.

## What a plugin sees without permissions

Context is filtered by what the plugin declared. Without `workspace.read`, folder and title fields are removed; without `session.read`, the session id is removed. What remains are ids, `kind`, `tool`, `status`, `running` and the language — enough to know which pane is focused and whether it is busy, not where you work or what you are doing.

```json
{
  "pane": { "id": 12, "kind": "claude", "tool": "claude", "status": "working", "running": true },
  "language": "en"
}
```

## The dialog is the boundary

`prompt.inject` with `target: "ask"` opens **Send to…**: you see the text, choose the agent and the destination, and nothing happens until you press **Send**. Other targets skip that dialog, which is why a plugin that acts on outside input should always use `ask`.

Terminals never have Enter pressed for them by `injectPrompt` — text is typed in and left for you.

## Links are treated as untrusted

`agentty://` links can come from anywhere, including a web page. For one minute after a link reaches a plugin, Agentty:

- routes that plugin's `injectPrompt` calls through **Send to…**, whatever target it asked for, and
- refuses `sendToTerminal` outright.

Clicks in the panel the link opened do not lift this. A link cannot turn one click into text typed into a terminal.

> [!IMPORTANT]
> If your plugin handles links, validate every parameter. The Cosmica plugin, for example, opens only `.md` files that are inside the Cosmica notes folder — a path outside it is refused.

## Rules for writing one

- **Ask only for the permissions you use.** Each one is shown to the user, and an unused permission costs you trust for nothing.
- **Never read or send credentials.** If you read another app's configuration, take only the fields you need — the Cosmica plugin reads `notes.path` and a local port, nothing else.
- **Keep your data in `AGENTTY_PLUGIN_DATA`.** Create files other applications read carefully, and never overwrite a user's file without being asked to.
- **Talk to `127.0.0.1` only**, unless the user configured a different endpoint themselves.
- **Check `pane.status` before typing.** Do not interrupt `working`, `permission` or `question`.
- **Validate anything that came from outside** — a link, a file, a server response — before acting on it.

## Rate limits and runaways

Agentty protects itself from a plugin that misbehaves, whether by accident or not:

| Limit | Behavior |
|---|---|
| Panel redraws | At most one every 50 ms |
| Notifications | At most one per 700 ms; extra calls are dropped and answered normally |
| Messages | More than 240 per second stops the plugin as a runaway |
| UI tree | 2 000 elements, 12 levels deep, 20 000 characters per text |
| Line length | 16 MB on stdout and stderr |

## Before you install someone else's plugin

- Read the permissions on the card. A note-taking plugin that wants `terminal.write` should explain why.
- Prefer plugins whose source you can read. The card's `links` usually point at the repository.
- After installing, **Logs** shows everything the plugin printed — a quick way to see what it actually does.
- **Disable** is instant, and **Uninstall** removes the plugin; its data folder stays until you delete it.
