---
title: 安装
description: 获取适用于 macOS、Windows 和 Linux 的 Agentty，并准备好配套的智能体 CLI。
---

Agentty 免费且无需账号。下载、拖入、打开终端即可。

## 下载

所有版本都发布在[发布页面](https://github.com/empty-user77/agentty-releases/releases)。

| 平台 | 文件 | 说明 |
|---|---|---|
| macOS 13+（Apple 芯片） | `Agentty-X.Y.Z-…-arm64.dmg` | 已签名并公证 |

打开 DMG，把 **Agentty** 拖进**应用程序**。由于已签名并公证，启动时不会出现 Gatekeeper 警告。

每个版本还附带 `Agentty-X.Y.Z-arm64.zip` 和可用于校验下载的 `SHA256SUMS.txt`。

> [!NOTE]
> **Windows 与 Linux 的构建尚未发布。** Agentty 在 macOS 上开发，目前也只有 macOS 提供下载。代码可在 Windows 10 1809 及以上和 Linux（X11 与 Wayland）上构建运行，两个平台的安装包也在计划中。差异请参见[平台](/docs/platforms)。

## 智能体命令行工具

Agentty 负责运行智能体，但不会捆绑它们。请自行安装所需工具，并确保它们在登录 shell 的 `PATH` 中可被找到。

- **Claude Code** —— `claude`
- **Codex** —— `codex`
- 可选：Gemini CLI、Copilot CLI、Cursor CLI、Grok、OpenCode、Qwen Code、Amp、Droid、Goose、Crush、Aider，以及本地的 Ollama 模型

Agentty 只检测并提供你已安装的工具。没装的不会先出现再失败，而是根本不显示。

> [!TIP]
> 如果某个工具在终端里能用、在 Agentty 里却不行，通常是因为 `PATH` 被写在只有交互式 shell 才读取的文件里。Agentty 通过登录 shell 启动窗格，请把 `PATH` 写在登录 shell 会读取的位置。

你也可以不使用 CLI 自带的登录方式，在**设置 → 账号**中用 API 密钥、网关令牌、`claude setup-token`、Amazon Bedrock、Google Vertex AI，或导入的 Codex `auth.json` 来启动。

### Node.js

用 JavaScript 编写的插件需要 `PATH` 中有 **Node.js 18 或更高版本**。Agentty 本身不需要。

### Git

git 页面、工作树和分支切换使用 `PATH` 中的 `git`。在 Windows 上，Claude Code 的钩子需要 **Git for Windows**。

## 更新

Agentty 在启动时和每小时检查一次发布频道。

在 macOS 上，它会下载已签名的 DMG、验证后替换应用并重新启动。

下载只会从 GitHub 托管的文件进行，并在使用前与公布的校验和比对验证。

## 从源码构建

源码仓库尚未公开。我们计划将其开源；在此之前，发布页面上的 macOS 构建就是运行 Agentty 的方式。

> [!NOTE]
> 在官方发布流水线之外制作的构建完全不会发送使用统计，因为分析凭据只存在于官方发布版中。参见[使用统计](/docs/telemetry)。

## Agentty 的文件位置

| 路径 | 用途 |
|---|---|
| `~/.agentty/settings.json` | 偏好设置 |
| `~/.agentty/workspaces.json` | 工作区、标签页、分屏和分组 |
| `~/.agentty/plugins/` | 已安装的插件 |
| `~/.agentty/handoffs/` | Session Flow 与对话迁移生成的上下文文档 |
| `~/.agentty/connectors.json` | API 连接器定义（密钥存放在系统凭据存储中） |

完整清单见[配置参考](/docs/configuration-reference)。

## 卸载

从应用程序文件夹删除 `Agentty.app` 即可。

若要一并清除数据，请删除 `~/.agentty`。智能体 CLI 写入的会话位于它们各自的文件夹（`~/.claude`、`~/.codex`），不归 Agentty 清理。

## 接下来

- [快速开始](/docs/quick-start) —— 最初的十分钟
- [平台](/docs/platforms) —— Windows 与 Linux 上的差异
