---
title: プラグイン クイックスタート
description: パネルとボタンとプロンプトを備えた Agentty プラグインを数分で作る — AI で、あるいは手作業で。
---

プラグインはマニフェストとプログラムが入ったフォルダです。Agentty は最初に使われたときそのプログラムを起動し、stdin/stdout でやり取りします。JSON-RPC 2.0 で、1 行につき JSON オブジェクト 1 つです。どの言語でも書けますが、Node.js SDK を使えば数行で済みます。

## AI で作る

**プラグイン**（パズルのアイコン）を開き、*自分で作る* の欄に名前とやらせたいことを書いて **Claude Code で作成してビルド**を押します。

Agentty がテンプレートからプラグインを作り（SDK、型定義、開発者ガイド、指示を書いた `CLAUDE.md`・`AGENTS.md`）、そのフォルダで Claude Code を開きます。**AI プロンプトをコピー**を押せば、同じプロンプトを他のエージェントでも使えます。

## 手作業で作る

プラグインのフォルダはこうなります。

```
~/.agentty/plugins/hello/
├── agentty-plugin.json   マニフェスト
├── main.mjs              プラグイン本体
└── agentty-plugin.mjs    SDK
```

SDK は依存関係のない 1 ファイルです。プラグインページで**開発者ガイド**を押すと、Agentty が SDK と型定義、資料を `~/.agentty/plugins/.sdk/` に展開します。そこから `agentty-plugin.mjs` を `main.mjs` の隣にコピーしてください。

### マニフェスト

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

`id` はフォルダ名と同じである必要があります。ほかは[マニフェスト リファレンス](/docs/plugin-manifest)にあります。

### プログラム

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

3 つのことが起きています。

1. `onPanelOpen` がパネルをツリーとして記述します。Agentty がネイティブに描くのでウェブビューはありません。
2. `onEvent('explain', …)` はその id のボタンが押されたときに動きます。
3. `command('hello.explain', …)` は同じ処理をコマンドパレットやペインバーのボタンから実行します。

### 読み込ませる

**プラグイン → 再読み込み**を押す（またはページを開き直す）と、プラグインがインストール済みとして現れ、パネルのボタンがタブ領域に、コマンドがパレット（⇧⌘P）とエージェントペインの上に出ます。

> [!WARNING]
> stdout に直接書かないでください。`console.log` は禁止です。stdout はプロトコルの通り道であり、JSON でない内容はログに記録されて無視されます。`plugin.log(...)` か `console.error(...)` を使ってください。

## 作業中の反復

- カードの**再起動**、またはパネルヘッダーの ↻ がコードの変更を反映します。
- カードの**ログ**は stderr、プロトコルエラー、クラッシュ、終了コードを表示します。
- **開発用にフォルダをリンク…** はコピーせずに自分のフォルダ（Git のチェックアウトなど）からプラグインを実行します。削除してもリンクが外れるだけです。
- **Claude Code で編集**はプラグインフォルダでワークスペースを開きます。

## 役に立つものにする

`context` は利用者がどこにいるかを教えます。

```js
plugin.onContextChange((context) => {
  plugin.log('focused pane:', context.pane?.kind, context.pane?.status);
});
```

エージェントに仕事を頼みつつ、行き先は利用者に決めさせます。

```js
await plugin.injectPrompt({
  text: 'Write release notes for the commits since the last tag.',
  title: 'Release notes',
  target: 'ask',      // 送信先… ダイアログを出します
  agent: 'claude',
  cwd: context.workspace?.cwd,
});
```

ペインの会話を読みます（`session.read` が必要）。

```js
const session = await plugin.getSession({ paneId: context.pane.id, maxTurns: 200 });
plugin.log(session.title, session.turns.length, 'turns');
```

> [!TIP]
> エージェントに要約やレポートといった成果物を作らせたいときは、プロンプトで指定したパスにファイルとして書くよう頼み、そのファイルを監視します。Cosmica プラグインがセッション要約を保存している方法です。

## 次に読むもの

- [マニフェスト リファレンス](/docs/plugin-manifest) — すべてのフィールド
- [Node.js SDK](/docs/plugin-sdk) — ハンドラ、呼び出し、UI ビルダー
- [プロトコル](/docs/plugin-protocol) — 他の言語で書く場合
- [権限](/docs/plugin-permissions) — 何を求め、何をしてはいけないか
- [配布する](/docs/plugin-publishing) — 他の人に共有する
