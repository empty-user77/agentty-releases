---
title: 清单参考
description: agentty-plugin.json 的所有字段 —— 标识信息、运行方式、权限、面板、命令与图标。
---

`agentty-plugin.json` 位于插件文件夹的根目录，告诉 Agentty 这个插件是什么、如何启动、能做什么，以及为界面添加了什么。

```json
{
  "id": "hello",
  "name": "Hello",
  "version": "0.1.0",
  "main": "main.mjs",
  "runtime": "node",
  "apiVersion": 1,
  "description": "Turns the current folder into a prompt.",
  "publisher": "you",
  "homepage": "https://example.com/hello",
  "permissions": ["prompt.inject"],
  "contributes": {
    "panel": { "title": "Hello", "icon": "sparkles" },
    "commands": [
      { "id": "hello.explain", "title": "Hello: Explain this folder", "icon": "bot", "paneBar": true }
    ]
  }
}
```

## 标识信息

| 字段 | 必填 | 说明 |
|---|---|---|
| `id` | 是 | 由 `a-z`、`0-9`、`-` 组成的 2–40 个字符。**必须与文件夹名相同。** |
| `name` | 是 | 显示在商店和面板按钮上 |
| `version` | 是 | `major.minor.patch` |
| `description` | | 商店卡片上的一行说明 |
| `publisher` | | 作者 |
| `homepage` | | 必须是 `https://` |
| `keywords` | | 用于商店搜索 |
| `links` | | 最多 6 个 `{ "label", "url" }`，作为按钮显示在卡片上（项目主页、文档、源码） |
| `icon` | | 下方列表中的图标名 |

## 运行

| 字段 | 默认值 | 说明 |
|---|---|---|
| `main` | 必填 | 相对于插件文件夹的入口点 |
| `runtime` | `node` | `node`（登录 shell `PATH` 中的 Node.js 18+）、`python`（`python3 <main>`）或 `executable`（直接运行 `<main>`） |
| `apiVersion` | `1` | 编写时依据的插件 API 版本 |
| `activationEvents` | `[]` | `["onStartup"]` 表示随 Agentty 一起启动，否则在首次使用时启动 |

Agentty 以插件文件夹作为工作目录启动程序。

## 与其他应用的集成

| 字段 | 说明 |
|---|---|
| `requires` | `{ "name", "url", "note" }` —— 该插件面向的应用或服务。卡片会显示是否检测到，未检测到时提供链接 |
| `detect` | 集成目标应用的路径（可用 `~`）。检测到则卡片标记为**推荐** |

## 权限

```json
"permissions": ["prompt.inject", "terminal.write", "session.read", "workspace.read"]
```

| 权限 | 允许的事 |
|---|---|
| `prompt.inject` | 发送提示词 |
| `terminal.write` | 向已打开的窗格输入 |
| `session.read` | 读取 AI 对话 |
| `workspace.read` | 列出工作区，查看上下文中的文件夹与标题字段 |

只申请你会用到的。清单会在安装前展示给用户，没有权限的调用会失败。参见[插件权限](/docs/plugin-permissions)。

## 插件添加的内容

### 面板

```json
"contributes": { "panel": { "title": "Hello", "icon": "sparkles" } }
```

会在标签栏添加按钮，并在终端右侧停靠一个面板（宽 360px，纵向滚动）。插件用 UI 树填充它 —— 参见 [SDK](/docs/plugin-sdk)。

### 命令

```json
"contributes": {
  "commands": [
    {
      "id": "hello.explain",
      "title": "Hello: Explain this folder",
      "description": "Sends a tour request to the focused agent",
      "icon": "bot",
      "paneBar": true,
      "when": "agent",
      "palette": true
    }
  ]
}
```

| 字段 | 说明 |
|---|---|
| `id` | 插件内唯一；SDK 以此 id 注册处理器 |
| `title` | 显示在命令面板中，前面加上插件名便于归类 |
| `description` | 可选的第二行 |
| `icon` | 窗格栏按钮的图标名 |
| `paneBar` | 为 `true` 时在 Claude Code / Codex 窗格上方的状态栏和分屏窗格标题中添加图标按钮 |
| `when` | 把窗格栏按钮限定为 `agent` 窗格、`shell` 窗格或 `always` |
| `palette` | 为 `false` 时从命令面板中隐藏 |

窗格栏命令收到的是**按下按钮的那个窗格**的上下文，而不是当前聚焦窗格的。

## 图标

`icon` 字段请使用下列名称，其他值会显示为拼图块。

```
app-window arrow-down arrow-left arrow-right arrow-up arrow-up-right at-sign bell bell-dot
blocks book-open bookmark bot brain bug calendar chart-column check chevron-down chevron-right
chevron-up circle-check circle-dot circle-pause circle-x clipboard clipboard-paste clock cloud
code columns-2 command container copy database download ellipsis external-link eye file-input
file-plus file-text folder folder-open folder-plus git-branch git-commit-horizontal
git-pull-request globe grip-vertical hammer hash history house image info key-round
layout-panel-left lightbulb link list list-tree loader-circle mail maximize-2 message-circle-question
message-square minimize-2 minus network notebook notebook-pen package panel-left-close
panel-left-open pencil picture-in-picture-2 play plug plus power puzzle refresh-cw rocket rotate-cw
rows-2 save scroll-text search send settings shield-alert sparkles square square-plus
square-terminal star sticky-note tag terminal trash-2 undo-2 unlink upload users wand-sparkles
workflow wrench x zap git-fork file lock graduation-cap
```

## 环境变量

插件程序会收到以下环境变量：

| 变量 | 含义 |
|---|---|
| `AGENTTY_PLUGIN_ID` | 插件的 id |
| `AGENTTY_PLUGIN_DIR` | 插件文件夹 |
| `AGENTTY_PLUGIN_DATA` | 存放设置与缓存的私有文件夹 |
| `AGENTTY_VERSION` | Agentty 版本 |
| `AGENTTY_LANGUAGE` | 用户语言（`en`、`ko`、`ja`、`zh`） |
| `AGENTTY_BIN` | `agentty` 命令行工具的路径 |

需要持久化的内容都请放在 `AGENTTY_PLUGIN_DATA` 里。
