---
title: 插件快速开始
description: 几分钟做出一个带面板、按钮和提示词的 Agentty 插件 —— 用 AI，或者手写。
---

插件是一个装着清单和程序的文件夹。Agentty 在插件首次被使用时启动该程序，并通过 stdin/stdout 与它对话：JSON-RPC 2.0，一行一个 JSON 对象。任何语言都可以写，用 Node.js SDK 只需几行。

## 用 AI 创建

打开**插件**（拼图图标），在*自己构建*下填写名称和你希望它做的事，点击**用 Claude Code 创建并构建**。

Agentty 会基于模板创建插件（SDK、类型定义、开发者指南，以及带有说明的 `CLAUDE.md` / `AGENTS.md`），并在该文件夹中打开 Claude Code。点击**复制 AI 提示词**可以把同样的提示词用在其他智能体上。

## 手写

插件文件夹长这样：

```
~/.agentty/plugins/hello/
├── agentty-plugin.json   清单
├── main.mjs              插件程序
└── agentty-plugin.mjs    SDK
```

SDK 是一个无依赖的单文件。在插件页面点击**开发者指南**，Agentty 会把 SDK、类型定义和文档解压到 `~/.agentty/plugins/.sdk/`，从那里把 `agentty-plugin.mjs` 复制到 `main.mjs` 旁边即可。

### 清单

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

`id` 必须与文件夹名相同。其余字段见[清单参考](/docs/plugin-manifest)。

### 程序

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

这里发生了三件事：

1. `onPanelOpen` 用一棵树描述面板。Agentty 原生绘制它，不需要网页视图。
2. `onEvent('explain', …)` 在该 id 的按钮被点击时运行。
3. `command('hello.explain', …)` 让同样的逻辑可以从命令面板或窗格栏按钮触发。

### 加载

点击**插件 → 刷新**（或重新打开页面）。插件会显示为已安装，面板按钮出现在标签栏，命令出现在命令面板（⇧⌘P）和智能体窗格上方。

> [!WARNING]
> 不要自己往 stdout 写东西，禁用 `console.log`。stdout 是协议通道，非 JSON 的内容会被记录并忽略。请使用 `plugin.log(...)` 或 `console.error(...)`。

## 开发时的循环

- 卡片上的**重启**或面板标题栏的 ↻ 会加载代码改动。
- 卡片上的**日志**显示 stderr、协议错误、崩溃和退出码。
- **链接开发文件夹…** 直接从你自己的文件夹（例如 Git 检出目录）运行插件，而不复制。卸载只会解除链接。
- **用 Claude Code 编辑**会在插件文件夹中打开一个工作区。

## 让它真正有用

`context` 告诉你用户在哪里：

```js
plugin.onContextChange((context) => {
  plugin.log('focused pane:', context.pane?.kind, context.pane?.status);
});
```

请智能体做事，但让用户决定送到哪里：

```js
await plugin.injectPrompt({
  text: 'Write release notes for the commits since the last tag.',
  title: 'Release notes',
  target: 'ask',      // 弹出“发送到…”对话框
  agent: 'claude',
  cwd: context.workspace?.cwd,
});
```

读取某个窗格中的对话（需要 `session.read`）：

```js
const session = await plugin.getSession({ paneId: context.pane.id, maxTurns: 200 });
plugin.log(session.title, session.turns.length, 'turns');
```

> [!TIP]
> 想让智能体产出摘要或报告这类成果，请在提示词中让它写入你指定的路径，然后监视那个文件。Cosmica 插件保存会话摘要用的就是这个办法。

## 接下来

- [清单参考](/docs/plugin-manifest) —— 每一个字段
- [Node.js SDK](/docs/plugin-sdk) —— 处理器、调用与 UI 构建器
- [协议](/docs/plugin-protocol) —— 用其他语言编写时
- [权限](/docs/plugin-permissions) —— 该申请什么，不该做什么
- [发布](/docs/plugin-publishing) —— 分享给别人
