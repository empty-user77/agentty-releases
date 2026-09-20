---
title: プラグイン プロトコル
description: SDK の背後でやり取りされる JSON-RPC のワイヤーフォーマット — どの言語でも Agentty プラグインを書くための仕様。
---

このページはワイヤーフォーマット API **バージョン 1** を説明します。JavaScript で書くなら [Node.js SDK](/docs/plugin-sdk) がすべて包んでくれます。どちらにせよ[クイックスタート](/docs/plugin-quickstart)を先に読むとよいでしょう。

## 通信方式

Agentty はプラグインフォルダを作業ディレクトリとしてプログラムを起動します。

| `runtime` | 実行コマンド |
|---|---|
| `node` | `node <main>` — ログインシェルの `PATH`、Homebrew、Volta、nvm の Node.js |
| `python` | `python3 <main>` |
| `executable` | `<main>` |

メッセージは [JSON-RPC 2.0](https://www.jsonrpc.org/specification) のオブジェクトで、**1 行に 1 つ**、UTF-8 で、stdin（Agentty → プラグイン）と stdout（プラグイン → Agentty）をやり取りします。16MB を超える行は拒否されます。stdout の JSON でない内容はログに残って無視され、stderr はプラグインログへ送られます。

stdin が閉じるか `shutdown` が来たら終了してください。`shutdown` から 1.5 秒経っても生きていると `SIGTERM`、さらに 1.5 秒後に `SIGKILL` が送られます。Agentty の終了時は両方が即座に続きます。

## Agentty → プラグイン

| メッセージ | 種類 | `params` |
|---|---|---|
| `initialize` | リクエスト — 応答が必要 | `{ apiVersion, agentty: { version }, plugin: { id, name, version, dir, dataDir }, language, context }` |
| `command/execute` | 通知 | `{ command, args, context }` |
| `panel/open` · `panel/close` | 通知 | `{ context }` |
| `ui/event` | 通知 | `{ element, event, value?, item?, action?, context }` |
| `context/changed` | 通知 | `{ context }` |
| `url/open` | 通知 | `{ path, query, url, context }` |
| `shutdown` | 通知 | `{}` |

`initialize` が最初に来て、その直後にプラグインを起動させたもの（コマンド、パネルを開く、リンク）が続きます。`initialize` には任意の結果で応答すれば十分です（例: `{}`）。

## プラグイン → Agentty

結果やエラーが必要ならリクエスト（`id` あり）として、不要なら通知（`id` なし）として送ります。

| メソッド | 権限 | `params` | 結果 |
|---|---|---|---|
| `ui/setPanel` | | `{ tree }` | `null` |
| `ui/showPanel` | | `{}` | `null` |
| `ui/notify` | | `{ message, kind }` — `info`、`success`、`warning`、`error` | `null` |
| `ui/setBadge` | | `{ text }`、最大 8 文字 | `null` |
| `context/get` | | `{}` | コンテキスト |
| `host/info` | | `{}` | `{ version, apiVersion, language }` |
| `host/openUrl` | | `{ url }` — http/https | `null` |
| `host/revealPath` | `workspace.read` | `{ path }` — 存在する絶対パス | `null` |
| `prompt/inject` | `prompt.inject` | `{ text, title?, target?, paneId?, workspaceId?, agent?, cwd?, submit? }` | `{ status: "asked" }` または `{ status: "sent", paneId }` |
| `terminal/send` | `terminal.write` | `{ paneId?, text, submit? }` — `paneId` がなければフォーカス中のペイン | `{ paneId }` |
| `session/get` | `session.read` | `{ paneId?, maxTurns? }` — 既定 200、最大 2000 | `{ paneId, agent, sessionId, title, cwd, status, turnCount, turns }` |
| `workspace/list` | `workspace.read` | `{}` | `[{ id, name, cwd, active, panes }]` |

700ms より速く届く `ui/notify` は正常に応答したうえで破棄され、毎秒 240 件を超えて送るプラグインは停止されます。コンテキストの項目はプラグインの権限で制限されます。

### エラーコード

| コード | 意味 |
|---|---|
| `-32601` | 未知のメソッド |
| `-32602` | 不正なパラメータ — UI ツリーの誤り、存在しないペインなど |
| `-32001` | 権限がない、またはリンク到達後 1 分間の制限中 |
| `-32002` | 利用できない — ウィンドウが開いていない、セッションがまだない |

## やり取りの例

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

`→` は Agentty からプラグイン、`←` はプラグインから Agentty です。リクエスト id は方向ごとに数えます。

## UI ツリー

すべてのノードは `type` を持つオブジェクトです。

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

イベント: `button` は `click`、`input` は `value` を伴う `change`・`submit`、`list` は `item` を伴う `select`（行のボタンは `item`・`action` を伴う `action`）、`choice` は選んだ値を伴う `change`、`toggle` は新しい真偽値を伴う `change` を送ります。

リスト項目の `tone` はアイコンの色を決め、`badge` と同じ値を使います。

## Agentty なしでテストする

プラグインは stdin を読み stdout に書く普通のプログラムなので、テストから直接動かせます。`initialize` リクエストを書き、試したい通知を続けて送り、プラグインが返す JSON を検証してください。
