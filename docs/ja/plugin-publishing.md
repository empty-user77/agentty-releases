---
title: 配布する
description: マーケットプレイスに登録するか、Git リポジトリやフォルダとして自分で配る。
---

プラグインを人に届ける方法は 2 つあり、どちらを使えるかはプラグインの種類で決まります。

| | |
|---|---|
| **マーケットプレイス** | ソースが公開された WebAssembly モジュール。**プラグイン → マーケットプレイス**に並び、ワンクリックでインストールされ、チェックサムで検証されます。 |
| **自分で配る** | プログラムとして動くものを含む、あらゆるプラグイン。利用者が自分で選んだ Git リポジトリやフォルダからインストールします。 |

## マーケットプレイス

[Agentty-Marketplace](https://github.com/empty-user77/Agentty-Marketplace) が Agentty の読む一覧です。プラグインはプルリクエストで追加されます。Agentty 自身のプラグインも同じ条件でそこにあります。アプリケーションに同梱されているものはありません。

一覧に載るのは**ソースが公開された WebAssembly モジュール**です。規則はそれだけで、両方が大事です。

- **WebAssembly** であること。Agentty 自身が実行するので、プロトコルが与えたものにしか触れません。プログラムとして動くプラグインはあなたの持つものすべてを持つので、Agentty はそれをインターネット上の一覧からはインストールしません。
- **ソースが公開**されていること。一覧にあるモジュールはバイナリだからです。誰でも何からビルドされたかを読み、ビルドし直せます。

### インストール時に起きること

1. Agentty が HTTPS で `index.json` を読みます。すべての項目をここで検査し直します。id、文言、権限、モジュールの配信元ホストまで。通らなかった項目は表示される代わりに一覧から外れます。
2. プラグインページが、それが何か、ソースはどこか、ライセンス、モジュールのサイズとチェックサム、そして**何ができるか**を完全な文で権限の下に見せます。
3. **インストール**を押すと、Agentty はモジュールをダウンロードしてそのチェックサムと照合します。一致するまでプラグインフォルダには何も入りません。

### 登録する

1. **モジュールをビルドして公開します。** ふつうはプラグインのリポジトリの GitHub リリースです。

   ```bash
   cargo build --release --target wasm32-unknown-unknown
   cp target/wasm32-unknown-unknown/release/your_plugin.wasm your-plugin.wasm
   shasum -a 256 your-plugin.wasm
   wc -c your-plugin.wasm
   ```

2. **項目を書きます。** `plugins/_template.json` を `plugins/<プラグイン id>.json` にコピーしてください。id はファイル名と `agentty-plugin.json` の `id` に一致させます。

   ```json
   {
     "id": "hello-world",
     "name": "Hello World",
     "version": "0.1.0",
     "publisher": "Your Name",
     "description": "One sentence about what it does.",
     "icon": "sparkles",
     "license": "MIT",
     "source": "https://github.com/you/agentty-hello-world",
     "keywords": ["example"],
     "apiVersion": 1,
     "surface": "sidebar",
     "mode": "push",
     "permissions": [],
     "module": {
       "url": "https://github.com/you/agentty-hello-world/releases/download/v0.1.0/hello-world.wasm",
       "sha256": "…16 進 64 文字…",
       "size": 93292
     }
   }
   ```

3. **検査してからプルリクエストを開きます。** CI が同じ検査を走らせます。

   ```bash
   python3 scripts/validate.py --download   # モジュールを取得してチェックサムを確認
   python3 scripts/validate.py --index      # index.json を再生成
   ```

### 項目のフィールド

| フィールド | |
|---|---|
| `id` | 2〜40 文字の `a-z 0-9 -`。ファイルは `plugins/<id>.json` |
| `name`, `version`, `description` | Agentty に表示されます。`version` は `major.minor.patch` |
| `publisher`, `license` | 作った人と、そのライセンス |
| `source` | モジュールをビルドした公開リポジトリ — **必須** |
| `homepage`, `keywords`, `icon` | 任意。アイコンは Agentty のアイコン集の名前 |
| `apiVersion` | モジュールが対象とするプラグインプロトコル。`1` なら省略 |
| `surface` | アイコンの位置: `sidebar`, `pane`（既定）, `status` |
| `mode` | パネルの開き方: `push`（既定）, `overlay`, `window`, `full` |
| `permissions` | 要求する権限。インストール前に示され、要求が増える更新でも再び示されます |
| `module.url` | github.com 上の `https://`。バージョンを含む必要があり、リリースを裏で差し替えられません |
| `module.sha256` | チェックサム。一致しないダウンロードは拒否されます |
| `module.size` | バイト単位のサイズ、最大 8 MB |

### 拒否されるもの

- `source` のリポジトリからビルドされていないモジュール、または誰も読めないリポジトリ。
- URL が配るものとチェックサムが一致しないもの。
- 使わない権限を求めるもの、あるいはその権限で何をするか説明にないもの。
- 他のプラグイン、他の配布者、または Agentty 自身になりすますもの。

### 更新

`version`, `module.url`, `module.sha256`, `module.size` を変えて、もう一度プルリクエストを開きます。インストール済みの全員に更新が提示されます。

インストール済みのバージョンより**多く**要求する更新は、何が増えるのかを示し、もう一度押させます。**すべて更新**はそれらを黙って通すのではなく除外し、いくつ残したかを伝えます。つまり権限を増やすと、見直さない利用者を失います。見直してもらえる説明を書いてください。

新しいバージョンが新しい Agentty にしかないものを使うなら、`apiVersion` も一緒に上げてください。古い Agentty の利用者は、実行できないモジュールを渡される代わりに、手元のバージョンを保ったまま更新を促されます。Agentty は実行できないバージョンを、インストール済みのものの更新として数えません。

項目を消すと Agentty はそのプラグインを提供しなくなります。入れている人にはそのまま残り、プラグインページから削除できます。

> [!NOTE]
> `AGENTTY_MARKETPLACE_INDEX` で別の一覧を指させば、一覧を書いている間に試せます。モジュールはリポジトリのリリース、または一覧と同じホストから配信できます。

## 自分で配る

以上はプラグインを使うのに必須ではありません。自分だけ、あるいは社内だけで使うプラグインは、その一覧を通る必要がありません。

### Git リポジトリとして

`agentty-plugin.json` をリポジトリの**ルート**に置いてください。利用者は*自分で作る*の下に `https://` の URL を貼り、**Git からインストール**を押します。

```
your-plugin/
├── agentty-plugin.json
├── main.mjs
├── agentty-plugin.mjs     同梱した SDK
├── README.md
└── LICENSE
```

> [!IMPORTANT]
> **SDK や `node_modules` を含め、動作に必要なものをすべて同梱してください。** Agentty は `npm install` を実行せず、エントリポイントをそのまま起動します。同梱されていない依存があるプラグインは、利用者のマシンで module-not-found エラーになり、ログに残るだけです。

できるだけ依存のないコードにしてください。SDK 自体に依存がないのも、まさにこの理由です。

### フォルダとして

**フォルダからインストール…** はフォルダを `~/.agentty/plugins/` にコピーします。正しい名前のフォルダに展開される zip も同じように動きます。フォルダ名はマニフェストの `id` と同じでなければなりません。

## バージョン

`version` は `major.minor.patch` です。公開のたびに上げてください。Agentty はこれを比べて、インストール済みのプラグインに更新があるかを判断します。

マニフェストとコードを揃えておいてください。使わなくなった権限はマニフェストからも外し、名前を変えたコマンドを `contributes` に残さないこと。

## 次に

- [Rust と WebAssembly](/docs/plugin-rust) — モジュールをビルドする
- [マニフェスト リファレンス](/docs/plugin-manifest) · [権限](/docs/plugin-permissions)
