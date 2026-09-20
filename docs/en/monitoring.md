---
title: Monitoring
description: What your AI work costs, which AI processes are running, and what your tabs talk to.
---

**⌥⌘U** opens Monitoring. Everything here is computed on your machine; nothing is uploaded.

## AI usage

Cost, calls, cache hit rate and tokens, broken down by model, project and tool. The numbers come from the transcript files the agent CLIs already wrote on your disk.

Claude model prices are built in. For other models — the ones Codex uses, for instance — add prices in `~/.agentty/pricing.json` and they are included:

```json
{
  "some-model": { "input": 1.25, "output": 10.0, "cacheRead": 0.125 }
}
```

Prices are USD per million tokens. `cacheRead`, `cacheWrite` and `cacheWrite1h` are optional and default to 10%, 125% and 200% of the input rate.

> [!NOTE]
> These are estimates for your own orientation, computed from local files. Your provider's invoice is the real number.

## Processes

The AI processes running on your machine right now, with what started them. Useful when something is still burning tokens after you thought it stopped.

## Proxy

A capture proxy that lists what your tabs talk to: start capture, open a tab, filter by endpoint.

- Tabs opened **while capture is on** are routed through a proxy running on your own machine.
- HTTPS stays encrypted. Only the host, port, byte counts and timing are recorded — no certificate is installed and nothing is decrypted.
- For plain HTTP, the method, path and status are recorded, never headers or bodies.
- Records live in memory, are limited in number, are never written to disk, and disappear when Agentty quits.

Stopping capture stops the recording. Tabs that were given the proxy keep using it so they don't lose their network.

## Docker

In a project with a compose file, a Dockerfile or containers of its own, the status bar shows how many of its containers run. Click it for a panel with each service's image, state and ports; start, stop, restart, `compose up -d` and `compose down` (asked twice, volumes kept), and logs in a new tab.

Environment values from compose files are never shown.
