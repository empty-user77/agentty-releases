---
title: Installation
description: Download Agentty for macOS, Windows or Linux, install the agent CLIs it drives, or build it from source.
---

Agentty is free and needs no account. Download it, drag it in, and start a terminal.

## Download

Every release is published on the [releases page](https://github.com/empty-user77/agentty-releases/releases).

**macOS 13 or later, Apple silicon** — `Agentty-X.Y.Z-…-arm64.dmg`. Open it and drag **Agentty** into **Applications**.

> [!NOTE]
> **Windows and Linux builds are not published yet.** Agentty is developed on macOS, and macOS is the only platform with a download today. The code builds and runs on Windows 10 1809+ and on Linux (X11 and Wayland), and packaged installers for both are planned — see [Platforms](/docs/platforms) for what differs there.

## Agent command line tools

Agentty runs the agents; it does not bundle them. Install the ones you want and make sure they are on the `PATH` of your login shell:

- **Claude Code** — `claude`
- **Codex** — `codex`
- Optional: Gemini CLI, Copilot CLI, Cursor CLI, Grok, OpenCode, Qwen Code, Amp, Droid, Goose, Crush, Aider, and local Ollama models

Agentty detects what you have installed and offers only those. Missing tools stay hidden instead of failing later.

> [!TIP]
> If an agent works in your terminal but not in Agentty, the usual cause is a `PATH` set in an interactive-only shell file. Agentty starts panes through your login shell, so put the `PATH` export where a login shell reads it.

You can also sign in without the CLI's own login — API key, gateway token, `claude setup-token`, Amazon Bedrock, Google Vertex AI, or an imported Codex `auth.json` — in **Settings → Accounts**.

### Node.js

Plugins written in JavaScript need **Node.js 18 or newer** on your `PATH`. Agentty itself does not require it.

### Git

The git page, working trees and branch switching use the `git` on your `PATH`. On Windows, Claude Code's hooks need **Git for Windows**.

## Updating

Agentty checks for new versions at launch and hourly. On macOS it installs the update and relaunches itself.

## Building from source

The source repository is not public yet. Opening it is planned; until then, the macOS build on the releases page is the way to run Agentty.

## Where Agentty keeps its files

| Path | Purpose |
|---|---|
| `~/.agentty/settings.json` | Preferences |
| `~/.agentty/workspaces.json` | Workspaces, tabs, splits and groups |
| `~/.agentty/plugins/` | Installed plugins |
| `~/.agentty/handoffs/` | Context documents from Session Flow and migrations |
| `~/.agentty/connectors.json` | API connector definitions (secrets live in the OS credential store) |

The full list is in the [configuration reference](/docs/configuration-reference).

## Uninstalling

- **macOS** — delete `Agentty.app` from Applications.
- **Windows** — Settings → Apps → Agentty → Uninstall.
- **Linux** — `sudo apt remove agentty` or `sudo dnf remove agentty`.

To remove your data as well, delete `~/.agentty`. Sessions written by the agent CLIs live in their own folders (`~/.claude`, `~/.codex`) and are not Agentty's to remove.

## Next

- [Quick start](/docs/quick-start) — the first ten minutes
- [Platforms](/docs/platforms) — what differs on Windows and Linux
