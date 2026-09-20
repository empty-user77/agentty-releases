---
title: マニフェスト リファレンス
description: agentty-plugin.json のすべてのフィールド — 識別情報、実行方法、権限、パネル、コマンド、アイコン。
---

`agentty-plugin.json` はプラグインフォルダの直下に置かれ、そのプラグインが何であり、どう起動し、何ができ、インターフェースに何を加えるかを Agentty に伝えます。

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

## 識別情報

| フィールド | 必須 | 説明 |
|---|---|---|
| `id` | はい | `a-z`、`0-9`、`-` からなる 2〜40 文字。**フォルダ名と一致させてください。** |
| `name` | はい | ストアとパネルボタンに表示 |
| `version` | はい | `major.minor.patch` |
| `description` | | ストアカードの 1 行説明 |
| `publisher` | | 作者 |
| `homepage` | | `https://` である必要があります |
| `keywords` | | ストア検索用 |
| `links` | | カードにボタンとして出る `{ "label", "url" }` を最大 6 件（プロジェクトサイト、ドキュメント、ソース） |
| `icon` | | 下記一覧のアイコン名 |

## 実行

| フィールド | 既定値 | 説明 |
|---|---|---|
| `main` | 必須 | プラグインフォルダからの相対パスのエントリポイント |
| `runtime` | `node` | `node`（ログインシェル `PATH` の Node.js 18+）、`python`（`python3 <main>`）、`executable`（`<main>` を直接実行） |
| `apiVersion` | `1` | 作成時に基準としたプラグイン API のバージョン |
| `activationEvents` | `[]` | `["onStartup"]` なら Agentty と同時に起動、なければ最初の使用時 |

Agentty はプラグインフォルダを作業ディレクトリとしてプログラムを起動します。

## 他のアプリとの連携

| フィールド | 説明 |
|---|---|
| `requires` | `{ "name", "url", "note" }` — このプラグインが対象とするアプリやサービス。カードが検出の有無を示し、なければリンクを出します |
| `detect` | 連携先アプリのパス（`~` 可）。見つかればカードに**おすすめ**が付きます |

## 権限

```json
"permissions": ["prompt.inject", "terminal.write", "session.read", "workspace.read"]
```

| 権限 | できること |
|---|---|
| `prompt.inject` | プロンプトの送信 |
| `terminal.write` | 開いているペインへの入力 |
| `session.read` | AI 会話の読み取り |
| `workspace.read` | ワークスペース一覧の取得、コンテキストのフォルダ・タイトル項目の参照 |

使うものだけを求めてください。一覧はインストール前に利用者へ表示され、権限のない呼び出しは失敗します。[プラグインの権限](/docs/plugin-permissions)を参照してください。

## プラグインが加えるもの

### パネル

```json
"contributes": { "panel": { "title": "Hello", "icon": "sparkles" } }
```

タブ領域にボタンが付き、ターミナルの右にパネルが入ります（幅 360px、縦スクロール）。プラグインが UI ツリーで中身を描きます — [SDK](/docs/plugin-sdk) を参照。

### コマンド

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

| フィールド | 説明 |
|---|---|
| `id` | プラグイン内で一意。SDK はこの id でハンドラを登録します |
| `title` | パレットに表示。プラグイン名を先頭に付けるとまとまって見えます |
| `description` | 任意の 2 行目 |
| `icon` | ペインバーのボタンのアイコン名 |
| `paneBar` | `true` で Claude Code・Codex ペイン上のステータスバーと分割ペインのヘッダーにアイコンボタンを追加 |
| `when` | ペインバーのボタンを `agent` ペイン、`shell` ペイン、`always` に限定 |
| `palette` | `false` でコマンドパレットから隠す |

ペインバーのコマンドは、フォーカス中のペインではなく**ボタンを押したペイン**のコンテキストを受け取ります。

## アイコン

`icon` には次の名前を使います。それ以外はパズルのピースとして表示されます。

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

## 環境変数

プラグインのプログラムには次の環境変数が渡されます。

| 変数 | 意味 |
|---|---|
| `AGENTTY_PLUGIN_ID` | プラグインの id |
| `AGENTTY_PLUGIN_DIR` | プラグインフォルダ |
| `AGENTTY_PLUGIN_DATA` | 設定とキャッシュのための専用フォルダ |
| `AGENTTY_VERSION` | Agentty のバージョン |
| `AGENTTY_LANGUAGE` | 利用者の言語（`en`、`ko`、`ja`、`zh`） |
| `AGENTTY_BIN` | `agentty` コマンドラインツールのパス |

保存するものはすべて `AGENTTY_PLUGIN_DATA` の中に置いてください。
