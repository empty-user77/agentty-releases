---
title: Configuration reference
description: Every file Agentty keeps, and every key in settings.json.
---

Agentty stores everything in `~/.agentty/`. Most of it is easier to change in **Settings** (⌘,).

## Files

| Path | Purpose |
|---|---|
| `~/.agentty/settings.json` | Preferences |
| `~/.agentty/workspaces.json` | Workspaces, tabs, splits and groups |
| `~/.agentty/themes/*.itermcolors` | Imported color themes |
| `~/.agentty/pricing.json` | Prices for non-Claude models |
| `~/.agentty/handoffs/` | Context documents from Session Flow and agent migration |
| `~/.agentty/connectors.json` | API connector definitions (no secrets) |
| `~/.agentty/agent-auth.json` | Sign-in method for new agent tabs (no secrets) |
| `~/.agentty/codex-home/` | Private Codex home for API-key sign-in |
| `~/.agentty/worktrees/` | Worktrees created per session |
| `~/.agentty/plugins/` | Installed plugins |
| `~/.agentty/plugin-data/<id>/` | Each plugin's own data |
| `~/.agentty/prompts/` | Long prompts handed to agents as files |
| `~/.agentty/install_id` | Random installation identifier for usage statistics |
| `agentty.json` (project) or `~/.agentty/commands.json` | Custom command palette entries |

Secrets are never in these files. They live in the operating system's credential store.

## settings.json

```json
{
  "language": "en",
  "theme": "Agentty Dark",
  "fontFamily": "JetBrains Mono",
  "fontSize": 13.0,
  "lineHeight": 1.25,
  "cursorShape": "block",
  "scrollback": 10000,
  "askDirectory": true,
  "autoWorktree": true,
  "agentTasks": true,
  "analytics": true,
  "agentBarPosition": "top"
}
```

| Key | Values |
|---|---|
| `language` | `en`, `ko`, `ja`, `zh` |
| `theme` | A built-in theme name, or an imported `.itermcolors` file name without the extension |
| `fontFamily`, `fontSize`, `lineHeight`, `padding` | Appearance |
| `cursorShape` | `block`, `beam`, `underline`; `cursorBlink` toggles blinking |
| `scrollback` | Lines kept per terminal |
| `askDirectory` | Ask for a folder for new workspaces; `askDirectoryForTabs` does the same for tabs |
| `resumeBar` | Offer earlier sessions when a terminal enters their folder |
| `autoWorktree` | A second session in a busy project gets its own worktree |
| `agentTasks` | Agents may ask to start parallel tasks; you confirm each one |
| `agentGuide` | Agents started here get a short guide to Agentty's commands |
| `stopServersOnClose` | Closing a pane stops the servers started in it |
| `confirmClose` | Ask before closing something that was used |
| `agentBarPosition` | `top` or `bottom` |
| `hud` | Status bar items in order: `model`, `context`, `usage`, `status`, `elapsed`, `links`, `spacer`, `ports`, `plugins`, `worktree`, `branch`, `folder` |
| `browser.autoOpenServers` | Open a dev server in the in-app browser once it answers |
| `linkOpener` | Whether ⌘-click opens links in-app or in your default browser |
| `externalEditor` | `auto`, `vsCode`, `cursor`, `system` |
| `harnessDetect`, `harnessPatterns`, `harnessSubmit`, `harnessAgent` | Harness detection and behavior |
| `systemNotifications`, `notifyWhenFocused`, `notifyAnswerRequests`, `chatNotify` | Notifications |
| `analytics` | Consent to anonymous usage statistics. `false`, or `DO_NOT_TRACK=1`, sends nothing |
| `menuBar` | Menu bar icon (macOS) |

## Custom commands

`agentty.json` in a project, or `~/.agentty/commands.json` for all projects, adds entries to the command palette:

```json
{
  "commands": [
    { "label": "Run tests", "command": "npm test" },
    { "label": "Deploy staging", "command": "./scripts/deploy.sh staging" }
  ]
}
```

## pricing.json

USD per million tokens, for models whose prices are not built in:

```json
{
  "some-model": { "input": 1.25, "output": 10.0, "cacheRead": 0.125 }
}
```

`cacheRead`, `cacheWrite` and `cacheWrite1h` are optional and default to 10%, 125% and 200% of `input`.
