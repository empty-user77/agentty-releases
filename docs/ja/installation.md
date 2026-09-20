---
title: インストール
description: macOS・Windows・Linux 向けの Agentty を入手し、一緒に使うエージェント CLI を用意する方法。
---

Agentty は無料でアカウントも不要です。ダウンロードしてドラッグし、ターミナルを開くだけです。

## ダウンロード

すべてのリリースは[リリースページ](https://github.com/empty-user77/agentty-releases/releases)で公開されます。

**macOS 13 以降、Apple シリコン** — `Agentty-X.Y.Z-…-arm64.dmg` を開き、**Agentty** を**アプリケーション**にドラッグします。

> [!NOTE]
> **Windows と Linux のビルドはまだ公開されていません。** Agentty は macOS で開発されており、現在ダウンロードがあるのも macOS だけです。コードは Windows 10 1809 以降と Linux（X11・Wayland）でビルドされ動作し、両プラットフォーム向けのパッケージ配布も予定しています。違いは[プラットフォーム](/docs/platforms)を参照してください。

## エージェントのコマンドラインツール

Agentty はエージェントを実行しますが、同梱はしません。使いたいツールをインストールし、ログインシェルの `PATH` から見つけられるようにしてください。

- **Claude Code** — `claude`
- **Codex** — `codex`
- 任意: Gemini CLI、Copilot CLI、Cursor CLI、Grok、OpenCode、Qwen Code、Amp、Droid、Goose、Crush、Aider、ローカルの Ollama モデル

Agentty はインストール済みのものだけを検出して提示します。ないツールは後で失敗する代わりに、そもそも表示されません。

> [!TIP]
> ターミナルでは動くのに Agentty でだけ動かない場合、たいていは `PATH` を対話シェルだけが読むファイルに設定しています。Agentty はログインシェルでペインを起動するため、ログインシェルが読む場所に `PATH` を書いてください。

CLI 自体のログインを使わずに、**設定 → アカウント**から API キー、ゲートウェイトークン、`claude setup-token`、Amazon Bedrock、Google Vertex AI、あるいは取り込んだ Codex の `auth.json` で開始することもできます。

### Node.js

JavaScript で書かれたプラグインには `PATH` 上の **Node.js 18 以降**が必要です。Agentty 自体には必要ありません。

### Git

git ページ、ワークツリー、ブランチ切り替えは `PATH` の `git` を使います。Windows では Claude Code のフックに **Git for Windows** が必要です。

## アップデート

Agentty は起動時と 1 時間ごとに新しいバージョンを確認します。macOS では更新をインストールし、自動で再起動します。

## ソースからのビルド

ソースリポジトリはまだ公開されていません。公開を予定しており、それまではリリースページの macOS ビルドが Agentty を動かす方法です。

## Agentty がファイルを置く場所

| パス | 用途 |
|---|---|
| `~/.agentty/settings.json` | 設定 |
| `~/.agentty/workspaces.json` | ワークスペース、タブ、分割、グループ |
| `~/.agentty/plugins/` | インストール済みプラグイン |
| `~/.agentty/handoffs/` | Session Flow と会話移行が作るコンテキスト文書 |
| `~/.agentty/connectors.json` | API コネクタの定義（秘密の値は OS の資格情報ストアに保管） |

全体の一覧は[設定リファレンス](/docs/configuration-reference)にあります。

## アンインストール

アプリケーションフォルダから `Agentty.app` を削除します。

データも消すには `~/.agentty` を削除してください。エージェント CLI が記録したセッションはそれぞれのフォルダ（`~/.claude`、`~/.codex`）にあり、Agentty が消すべきものではありません。

## 次に読むもの

- [クイックスタート](/docs/quick-start) — 最初の 10 分
- [プラットフォーム](/docs/platforms) — Windows と Linux での違い
