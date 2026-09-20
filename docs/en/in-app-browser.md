---
title: In-app browser
description: A browser panel beside your terminals, dev servers that open themselves, and letting an agent drive the page.
---

**⇧⌘B** opens a browser panel next to your terminals. On macOS it is a WebKit view inside Agentty; on Windows and Linux links open in your default browser instead.

## Opening things in it

- ⌘-click a link in any terminal (Ctrl-click on Windows and Linux).
- **Settings → General** decides whether ⌘-click opens links here or in your default browser.
- Type a URL, or a search term — the search engine is configurable.

## Dev servers

A local server started in a tab appears as a `:port` chip in the workspace list and opens in the panel as soon as it answers with a page.

Closing the tab that started a server stops the server. Turn either behavior off in **Settings → General** and **Settings → Browser**.

## Letting an agent drive it

Claude Code and Codex can control this browser through MCP: click, type, read the console, take a screenshot. It is how an agent checks its own work on a running page.

The tools an agent gets are:

| Tool | What it does |
|---|---|
| `browser_open` | Show the panel, optionally loading a URL |
| `browser_navigate` | Load a URL, `host:port`, or search words |
| `browser_status` | Current URL, title, and whether the page is still loading |
| `browser_wait_load` | Wait until loading finishes |
| `browser_click` | Click an element |
| `browser_console` | Read the page's console output |
| `browser_screenshot` | Capture the page |
| `browser_close` | Close the panel |

They are registered with the agent when Agentty starts it, so there is nothing to install or configure. A typical loop is: the agent starts the dev server, opens the page, clicks through the change it just made, reads the console for errors, and fixes what it finds.

> [!IMPORTANT]
> The browser keeps the sessions you are signed into. An agent driving it acts inside those sessions. Enable it when you want that, and be aware of which tabs are open.
