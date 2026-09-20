---
title: 插件协议
description: SDK 背后的 JSON-RPC 线上格式 —— 用任何语言编写 Agentty 插件的规范。
---

本页描述线上格式 API **版本 1**。若用 JavaScript 编写，[Node.js SDK](/docs/plugin-sdk) 已经封装了全部内容；无论哪种方式，建议先读[快速开始](/docs/plugin-quickstart)。

## 传输方式

Agentty 以插件文件夹作为工作目录启动程序：

| `runtime` | 启动命令 |
|---|---|
| `node` | `node <main>` —— 来自登录 shell `PATH`、Homebrew、Volta 或 nvm 的 Node.js |
| `python` | `python3 <main>` |
| `executable` | `<main>` |

消息是 [JSON-RPC 2.0](https://www.jsonrpc.org/specification) 对象，**每行一个**，UTF-8 编码，经由 stdin（Agentty → 插件）和 stdout（插件 → Agentty）传递。超过 16MB 的行会被拒绝。stdout 上非 JSON 的内容会被记录并忽略，stderr 进入插件日志。

当 stdin 关闭或收到 `shutdown` 时请退出。`shutdown` 后 1.5 秒仍在运行会收到 `SIGTERM`，再过 1.5 秒收到 `SIGKILL`。Agentty 退出时两者会立即依次发出。

## Agentty → 插件

| 消息 | 类型 | `params` |
|---|---|---|
| `initialize` | 请求 —— 需要应答 | `{ apiVersion, agentty: { version }, plugin: { id, name, version, dir, dataDir }, language, context }` |
| `command/execute` | 通知 | `{ command, args, context }` |
| `panel/open` · `panel/close` | 通知 | `{ context }` |
| `ui/event` | 通知 | `{ element, event, value?, item?, action?, context }` |
| `context/changed` | 通知 | `{ context }` |
| `url/open` | 通知 | `{ path, query, url, context }` |
| `shutdown` | 通知 | `{}` |

`initialize` 最先到达，紧接着是启动该插件的那件事（命令、打开面板或链接）。对 `initialize` 用任意结果应答即可，例如 `{}`。

## 插件 → Agentty

需要结果或错误时作为请求发送（带 `id`），不关心结果时作为通知发送（不带 `id`）。

| 方法 | 权限 | `params` | 结果 |
|---|---|---|---|
| `ui/setPanel` | | `{ tree }` | `null` |
| `ui/showPanel` | | `{}` | `null` |
| `ui/notify` | | `{ message, kind }` —— `info`、`success`、`warning`、`error` | `null` |
| `ui/setBadge` | | `{ text }`，最多 8 个字符 | `null` |
| `context/get` | | `{}` | 上下文 |
| `host/info` | | `{}` | `{ version, apiVersion, language }` |
| `host/openUrl` | | `{ url }` —— http/https | `null` |
| `host/revealPath` | `workspace.read` | `{ path }` —— 存在的绝对路径 | `null` |
| `prompt/inject` | `prompt.inject` | `{ text, title?, target?, paneId?, workspaceId?, agent?, cwd?, submit? }` | `{ status: "asked" }` 或 `{ status: "sent", paneId }` |
| `terminal/send` | `terminal.write` | `{ paneId?, text, submit? }` —— 不带 `paneId` 时为聚焦窗格 | `{ paneId }` |
| `session/get` | `session.read` | `{ paneId?, maxTurns? }` —— 默认 200，最大 2000 | `{ paneId, agent, sessionId, title, cwd, status, turnCount, turns }` |
| `workspace/list` | `workspace.read` | `{}` | `[{ id, name, cwd, active, panes }]` |

到达速度快于每 700ms 一条的 `ui/notify` 会被丢弃（但仍正常应答），每秒发送超过 240 条消息的插件会被停止。上下文字段受插件权限限制。

### 错误码

| 代码 | 含义 |
|---|---|
| `-32601` | 未知方法 |
| `-32602` | 参数无效 —— UI 树有误、窗格不存在等 |
| `-32001` | 缺少权限，或处于链接到达后一分钟的限制期内 |
| `-32002` | 不可用 —— 没有打开的窗口、尚无会话 |

## 交互示例

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

`→` 表示 Agentty 发给插件，`←` 表示插件发给 Agentty。请求 id 按方向各自计数。

## UI 树

每个节点都是带 `type` 的对象：

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

事件：`button` 发送 `click`；`input` 发送带 `value` 的 `change` 与 `submit`；`list` 发送带 `item` 的 `select`，行内按钮发送带 `item` 和 `action` 的 `action`；`choice` 发送带选项值的 `change`；`toggle` 发送带新布尔值的 `change`。

列表项的 `tone` 决定其图标颜色，取值与 `badge` 相同。

## 不用 Agentty 也能测试

插件只是一个读取 stdin、写入 stdout 的普通程序，因此可以从测试中直接驱动：先写一个 `initialize` 请求，再发送你想验证的通知，然后对插件写回的 JSON 做断言。
