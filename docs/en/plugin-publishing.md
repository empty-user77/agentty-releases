---
title: Publishing a plugin
description: Ship a plugin as a Git repository or a folder, version it, and keep it working as Agentty changes.
---

There is no central registry. A plugin is a folder with a manifest, so publishing one means putting that folder where people can get it.

## Share it as a Git repository

Put `agentty-plugin.json` at the **root** of the repository. Users paste the `https://` URL under *Build your own* → **Install from Git**.

```
your-plugin/
├── agentty-plugin.json
├── main.mjs
├── agentty-plugin.mjs     the SDK, bundled
├── README.md
└── LICENSE
```

> [!IMPORTANT]
> **Bundle everything the plugin needs to run, including the SDK and any `node_modules`.** Agentty does not run `npm install` — it starts your entry point as it is. A plugin with an unbundled dependency fails on the user's machine with a module-not-found error in its log.

Prefer dependency-free code where you can. The SDK itself has no dependencies for exactly this reason.

## Share it as a folder

**Install from Folder…** copies the folder into `~/.agentty/plugins/`. A zip that unpacks to a correctly named folder works the same way — the folder name must equal the `id` in the manifest.

## Versioning

`version` is `major.minor.patch`. Agentty shows it on the card and uses it to offer an **Update** when a built-in plugin ships a newer version with Agentty.

For plugins installed from Git or a folder, the user updates by reinstalling. Say in your README how you expect that to happen, and keep the manifest's `links` pointing at a page where people can see what changed.

`apiVersion` says which plugin API version you wrote against — currently `1`. Leave it at the version you tested with rather than following the newest number blindly.

## A README that helps

The store card shows `description` and up to six `links`. Everything else people need belongs in the README:

- what it integrates with, and what has to be installed for it to work
- which permissions it asks for and **why** — the single most useful line you can write
- what it stores, and where
- how to report a problem

If your plugin integrates with another application, declare it so the card can say whether it was found:

```json
{
  "requires": {
    "name": "Cosmica",
    "url": "https://www.cosmica.ink/",
    "note": "Needed to read and write notes"
  },
  "detect": ["~/Applications/Cosmica.app", "/Applications/Cosmica.app"]
}
```

When `detect` matches, the card is marked **Recommended** for that user.

## Test before you publish

- **Link Folder for Development…** runs the plugin from your working copy, so you can edit and press **Restart** without reinstalling.
- **Logs** on the card shows stderr, protocol errors, crashes and exit codes.
- Drive the plugin from a test harness: it is an ordinary program reading JSON lines on stdin and writing them on stdout, so a test can send `initialize`, then the notifications you want to exercise, and assert on what comes back.
- Try it with the permissions you actually declare. A call without its permission fails with `-32001`, and it is easy to miss that while developing with more access than you ship.
- Check both themes and a long panel — the panel is 360 px wide and scrolls.

## Keeping it working

- Handle `shutdown` and exit; a plugin still alive 1.5 seconds later is terminated.
- Never write to stdout except protocol messages. Use `plugin.log(...)` or `console.error(...)`.
- Treat every field of the context as optional. Permissions, and the state of the window, decide what is present.
- New UI element types and context fields may appear in later versions. Ignore what you do not know rather than failing on it.

## Built-in plugins

Plugins that ship inside Agentty update with the application, and their cards show **Recommended** when the app they integrate with is installed. If you think your plugin belongs there, open an issue on the Agentty releases repository describing what it does and who it is for.
