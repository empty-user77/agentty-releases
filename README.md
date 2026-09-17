# Agentty

<p align="center">
  <img src="assets/banner.png" alt="Agentty — The orchestration terminal for AI-native workflows" width="100%" />
</p>

<p align="center">
  <b>AI NATIVE · PARALLEL AGENTS · NATIVE RUST</b><br/>
  <i>The real terminal for the AI Native era.</i>
</p>

<p align="center">
  <a href="https://agentty.run">Website</a> ·
  <a href="https://github.com/empty-user77/agentty-releases/releases/latest">Download</a> ·
  <a href="#english">English</a> ·
  <a href="#한국어">한국어</a> ·
  <a href="#日本語">日本語</a> ·
  <a href="#中文-简体">中文</a>
</p>

<p align="center">
  <a href="https://github.com/empty-user77/agentty-releases/releases/latest"><img alt="Latest release" src="https://img.shields.io/github/v/release/empty-user77/agentty-releases?label=release&color=f5c518"></a>
  <img alt="Platform: macOS 13+" src="https://img.shields.io/badge/platform-macOS%2013%2B-lightgrey.svg">
  <img alt="Apple Silicon" src="https://img.shields.io/badge/Apple%20Silicon-arm64-lightgrey.svg">
  <img alt="Size: 17MB" src="https://img.shields.io/badge/size-17MB-brightgreen.svg">
</p>

<p align="center">
  <img src="assets/shots/workspace.webp" alt="Agentty workspace — four agents running side by side" width="100%" />
</p>

---

## English

### The real terminal for the AI Native era

Run every AI agent in one window, see what each is doing, and share context between them.

Agentty is a native terminal app for running AI coding agents such as **Claude Code**, **Codex**, Gemini CLI, Amp and OpenCode. It shows what each agent is doing, runs as many agents as you want in split panes, and lets sessions share context with each other.

> 17MB · 0 Electron · 13+ agent CLIs · 1 window for all your agents

### Why Agentty — a terminal made for running agents

- **Knows your agents** — It can tell whether an agent is working, finished, or waiting for you.
- **Runs them side by side** — Workspaces, tabs and split panes, so several agents fit in one window.
- **Manages sessions** — Resume old sessions, move them between Claude Code and Codex, or link them up.
- **Shows what's going on** — Processes, usage, git and subagents each get their own screen.
- **Small and quick** — 17MB, written in Rust and drawn on the GPU. It opens right away.

### 01 · AI Native — it knows what your agents are doing

Agentty receives status events from Claude Code and Codex, so it knows when an agent finishes a turn, asks for permission, or needs an answer. The pane gets a highlighted border, the workspace gets a badge, and you get a desktop notification that opens the right pane when you click it.

- Status for every agent: working, done, waiting, or needs permission
- The highlight stays on until you actually look at the pane
- Each pane shows the model, context usage, rate limits and git branch
- See which subagents are running and what they last did
- `cd` into a project and it offers to resume the last session there

### 02 · Parallel — run as many agents as you want in one window

There's no limit on how many panes or agents you open. Split a tab and start a different agent in each pane. For example, Opus can work on the main change while Codex writes tests and Gemini updates the docs. Workspaces remember their tabs, panes and folders, so after a restart everything is where you left it.

- **Workspaces, tabs, splits** — Group them, drag them around, rename them. Layouts are restored.
- **Multiple windows** — Each window has its own layout and its own mini mode.
- **Dev server ports** — Ports your processes listen on show up under the workspace.
- **A browser your agents can use** — A built‑in MCP server lets Claude Code and Codex click, type and take screenshots. You can turn it off in settings.

### 03 · Session manager — share context between agents

Agentty reads the session files Claude Code and Codex already write on your computer. You can search them, resume any of them, hand a conversation from Claude to Codex, or connect two sessions so one sees what the other is doing.

- **Session Flow** — Drag from one Claude Code or Codex session to another to send over its context.
- **Live links** — New turns get forwarded as they happen. Reply loops are blocked.
- **Two‑way links** — Both sessions see each other, so two agents can work on one problem.
- **Claude ⇄ Codex** — Move a conversation to the other agent and back again.
- **Search** — Look up sessions by title, message text or folder (`⇧⌘O`).
- **Resume** — Continue a session in a new workspace or in a new tab.

### 04 · Visual — everything has a screen you can look at

You shouldn't need `ps aux | grep claude` to find out what your agents are up to. These pages are part of the app.

- **AI Processes** — CPU, memory and disk for each agent and session, including MCP servers and child processes. Refreshes every 2 seconds.
- **Mini mode** — Shrinks the window into a small panel that stays on top. When an agent finishes, a speech bubble pops up.
- **Menu bar** — Weekly usage, time until limits reset, and the list of running agents.
- **AI Usage** — Cost, calls, cache hits, tokens, models and tools, calculated from local transcripts.
- **AgentGit** — Git is built in, so you can manage the repo of the session you're looking at. Review diffs, commit, switch branches and merge without leaving the app.
- **In‑app browser** — Open your dev server next to the terminal and see changes as soon as they land. Claude Code and Codex can drive the same browser over MCP while they build and test.
- **Extensions & MCP** — Skills, subagents, commands, plugins and MCP servers in one list. Official MCP servers can be added with one click.
- **API connectors** — Expose any HTTP API to your agents as an MCP tool.

### 05 · Native — 17MB, and it opens right away

There's no Electron or bundled Chromium. Agentty is written in Rust, uses [GPUI](https://gpui.rs) to render with Metal, and uses `alacritty_terminal` for the terminal itself. It leaves your CPU and memory for the agents.

### Works with the agents you already have installed

Claude Code and Codex have the deepest integration, including live status, session management and context sharing. Gemini CLI, Antigravity CLI, Amp, OpenCode, Copilot CLI, Cursor Agent, Qwen Code, Droid, Goose, Crush, Aider and local Ollama models are detected and can be launched from the app.

### Coming soon

- **Idea mode** — One flow from idea to development to launching your service, all inside Agentty.
- **Plugins** — Add your own features to Agentty with plugins.
- **Linux & Windows** — Agentty on more platforms than macOS.
- **Open source** — The full source code, published on GitHub.

### Your conversations stay on your computer

Session lists, usage numbers and handoffs are built from transcript files on your computer, and Agentty doesn't send your conversations anywhere. Agent status events and handoff files are only accessible to your user account. Apart from APIs and MCP servers you add yourself, the app only goes online to check for updates and service status. The agent CLIs still talk to their own providers as usual.

- Conversations stay local · Secrets in Keychain · Owner‑only file access · Open source soon

### Getting Started

- **Download** — Grab the latest `.dmg` from [Releases](https://github.com/empty-user77/agentty-releases/releases/latest) and drag `Agentty.app` into `/Applications`. Signed and notarized by Apple.
- **Auto-update** — Agentty checks for new versions at launch and every hour. Updates are verified, installed and relaunched for you.
- **Requirements** — macOS 13 Ventura or later, Apple Silicon (M1/M2/M3/M4). `claude` and/or `codex` on your `PATH` for agent tabs.
- **Support** — Report bugs or send feedback via [Issues](https://github.com/empty-user77/agentty-releases/issues).

---

## 한국어

### AI Native를 위한 진짜 터미널

모든 AI 에이전트를 한 창에서 돌리고, 상태를 한눈에 보고, 컨텍스트까지 주고받으세요.

Agentty는 **Claude Code**, **Codex**, Gemini CLI, Amp, OpenCode 같은 AI 코딩 에이전트를 돌리기 위한 네이티브 터미널 앱입니다. 에이전트마다 무엇을 하고 있는지 보여주고, 분할창으로 원하는 만큼 에이전트를 띄울 수 있고, 세션끼리 컨텍스트를 주고받을 수 있어요.

> 17MB · Electron 0 · 에이전트 CLI 13+ · 모든 에이전트를 한 창에

### 왜 Agentty인가 — 에이전트를 돌리는 데 맞춘 터미널

- **에이전트 상태를 압니다** — 작업 중인지, 끝났는지, 내 입력을 기다리는지 알려줍니다.
- **여러 개를 나란히** — 워크스페이스, 탭, 분할창으로 한 창에서 여러 에이전트를 돌립니다.
- **세션 관리** — 지난 세션을 이어 하고, Claude Code와 Codex 사이에서 옮기거나 연결합니다.
- **화면으로 확인** — 프로세스, 사용량, Git, 서브에이전트를 각각 화면으로 볼 수 있어요.
- **작고 빠릅니다** — Rust로 만들고 GPU로 그립니다. 17MB라 켜는 데 오래 안 걸려요.

### 01 · AI Native — 에이전트가 뭘 하는지 터미널이 압니다

Claude Code와 Codex에서 상태 이벤트를 받아서, 에이전트가 답변을 끝냈는지, 권한을 요청하는지, 질문에 답을 기다리는지 알아챕니다. 그러면 해당 분할창 테두리가 켜지고 워크스페이스에 배지가 붙고, 데스크톱 알림이 옵니다. 알림을 누르면 그 창으로 바로 이동해요.

- 에이전트마다 작업 중, 완료, 대기, 권한 요청 상태 표시
- 직접 확인하기 전까지 강조 테두리가 꺼지지 않음
- 분할창마다 모델, 컨텍스트 사용량, 사용 한도, 브랜치 표시
- 실행 중인 서브에이전트와 마지막 작업 내역 확인
- 프로젝트 폴더로 `cd`하면 거기서 하던 세션을 이어 할지 물어봄

### 02 · 병렬 작업 — 한 창에서 에이전트를 원하는 만큼

분할창과 에이전트 개수에는 제한이 없어요. 탭을 나누고 칸마다 다른 에이전트를 띄우면 됩니다. 예를 들어 Opus가 메인 작업을 하는 동안 Codex는 테스트를, Gemini는 문서를 맡을 수 있어요. 워크스페이스는 탭, 분할창, 폴더 구성을 기억하고 있어서 앱을 다시 켜도 그대로 돌아옵니다.

- **워크스페이스, 탭, 분할창** — 그룹으로 묶고, 드래그로 옮기고, 이름을 바꿀 수 있어요. 레이아웃도 복원됩니다.
- **여러 창** — 창마다 레이아웃과 미니모드가 따로 있어요.
- **개발 서버 포트** — 열려 있는 포트가 워크스페이스 아래에 표시됩니다.
- **에이전트가 쓰는 브라우저** — 내장 MCP 서버로 Claude Code와 Codex가 직접 클릭, 입력, 스크린샷을 합니다. 설정에서 끌 수 있어요.

### 03 · 세션 매니저 — 에이전트끼리 컨텍스트 주고받기

Claude Code와 Codex가 PC에 남기는 공식 세션 기록을 그대로 읽어옵니다. 세션을 검색하고 이어 할 수 있고, Claude에서 하던 대화를 Codex로 넘기거나, 두 세션을 연결해서 한쪽 작업 내용을 다른 쪽이 보게 할 수도 있어요.

- **다중 AI모델 통신** — Claude Code나 Codex 세션에서 다른 세션으로 선을 끌어다 놓으면 컨텍스트가 넘어갑니다.
- **실시간 연결** — 새 대화가 생길 때마다 자동으로 전달해요. 서로 계속 주고받는 루프는 막아둡니다.
- **양방향 연결** — 두 세션이 서로의 내용을 보면서 같은 문제를 같이 풀 수 있어요.
- **Claude ⇄ Codex** — 대화를 다른 에이전트로 옮기고, 필요하면 다시 가져옵니다.
- **검색** — 제목, 대화 내용, 폴더 경로로 세션을 찾습니다 (`⇧⌘O`).
- **이어 하기** — 새 워크스페이스나 새 탭에서 세션을 이어 합니다.

### 04 · 시각화 — 궁금한 건 화면으로 바로 확인

에이전트가 뭘 하는지 보려고 `ps aux | grep claude`를 칠 필요가 없게, 필요한 화면을 앱 안에 넣었습니다.

- **AI 프로세스** — 에이전트와 세션별 CPU, 메모리, 디스크 사용량이에요. MCP 서버와 자식 프로세스까지 포함하고 2초마다 갱신됩니다.
- **미니모드** — 창을 작은 패널로 줄여 항상 위에 띄워둡니다. 에이전트가 작업을 끝내면 말풍선으로 알려줘요.
- **메뉴바** — 주간 사용량, 한도 초기화까지 남은 시간, 실행 중인 에이전트 목록을 볼 수 있어요.
- **AI 사용량** — 비용, 호출 수, 캐시 적중률, 토큰, 모델, 도구 사용을 로컬 기록으로 계산합니다.
- **AgentGit** — Git이 내장되어 있어서 보고 있는 세션의 저장소를 바로 형상관리할 수 있어요. diff 확인, 커밋, 브랜치 전환, 머지까지 앱 안에서 합니다.
- **인앱 브라우저** — 터미널 옆에 개발 서버를 띄워두고 결과를 바로 확인하세요. Claude Code와 Codex도 MCP로 같은 브라우저를 직접 조작하면서 개발하고 테스트합니다.
- **확장 & MCP** — 스킬, 서브에이전트, 커맨드, 플러그인, MCP 서버를 한 목록에서 관리해요. 공식 MCP 서버는 클릭 한 번으로 추가할 수 있어요.
- **API 커넥터** — HTTP API를 MCP 도구로 만들어 에이전트가 쓰게 합니다.

### 05 · 네이티브 — 17MB, 켜자마자 바로 씁니다

Electron이나 Chromium을 넣지 않았습니다. Rust로 작성했고, 화면은 [GPUI](https://gpui.rs)로 Metal에서 그리고, 터미널 처리는 `alacritty_terminal`을 씁니다. CPU와 메모리는 에이전트가 쓰도록 남겨둬요.

### 이미 설치해 둔 에이전트를 그대로 씁니다

Claude Code와 Codex는 실시간 상태, 세션 관리, 컨텍스트 공유까지 가장 깊게 연동됩니다. Gemini CLI, Antigravity CLI, Amp, OpenCode, Copilot CLI, Cursor Agent, Qwen Code, Droid, Goose, Crush, Aider와 로컬 Ollama 모델은 자동으로 감지해서 앱에서 바로 실행할 수 있어요.

### 출시 예정

- **아이디어 모드** — 아이디어에서 개발, 서비스 출시까지 Agentty 안에서 한 번에 이어지는 올인원 모드.
- **플러그인** — 플러그인으로 Agentty에 원하는 기능을 직접 추가할 수 있게 됩니다.
- **Linux · Windows** — macOS 외에 Linux와 Windows에서도 쓸 수 있게 준비하고 있어요.
- **오픈소스 공개** — 전체 소스 코드를 GitHub에 공개합니다.

### 대화 기록은 내 PC에만 있습니다

세션 목록, 사용량, 대화 넘기기는 PC에 있는 기록 파일로 만들고, Agentty가 대화 내용을 외부로 보내지 않아요. 에이전트 상태 이벤트와 대화 넘기기 파일은 본인 계정만 접근할 수 있습니다. 직접 추가한 API나 MCP 서버를 빼면, 앱이 인터넷에 접속하는 건 업데이트 확인과 서비스 상태 확인뿐이에요. 에이전트 CLI는 원래처럼 각자의 서비스와 통신합니다.

- 대화는 로컬에만 · 키는 키체인에 저장 · 본인만 접근 가능한 파일 · 오픈소스 공개 예정

### 시작하기

- **다운로드** — [Releases](https://github.com/empty-user77/agentty-releases/releases/latest) 페이지에서 최신 `.dmg`를 받아 `Agentty.app`을 `/응용 프로그램`으로 드래그하면 끝입니다. Apple 서명·공증을 거친 빌드입니다.
- **자동 업데이트** — 앱 실행 시와 1시간마다 새 버전을 확인합니다. 검증된 업데이트를 설치하고 자동으로 재시작합니다.
- **시스템 요구사항** — macOS 13 Ventura 이상, Apple Silicon (M1/M2/M3/M4). 에이전트 탭을 쓰려면 `PATH`에 `claude` 또는 `codex`가 있어야 합니다.
- **문의** — 버그 제보나 피드백은 [Issues](https://github.com/empty-user77/agentty-releases/issues)에 남겨주세요.

---

## 日本語

### AI Nativeのための本物のターミナル

すべてのAIエージェントをひとつの画面で動かし、状態を確認し、コンテキストを共有できます。

Agenttyは、**Claude Code**、**Codex**、Gemini CLI、Amp、OpenCodeなどのAIコーディングエージェントを動かすためのネイティブターミナルアプリです。各エージェントの状態を表示し、分割ペインで好きなだけエージェントを起動でき、セッション同士でコンテキストを共有できます。

> 17MB · Electron 0 · エージェントCLI 13+ · すべてのエージェントをひとつの画面に

### Agenttyを選ぶ理由 — エージェントを動かすためのターミナル

- **状態が分かる** — 作業中か、完了したか、入力を待っているかを知らせます。
- **並べて動かせる** — ワークスペース、タブ、分割ペインで複数のエージェントをひとつの画面に。
- **セッション管理** — 過去のセッションを再開し、Claude CodeとCodexの間で移したりつないだりできます。
- **画面で確認** — プロセス、使用量、Git、サブエージェントをそれぞれの画面で見られます。
- **軽くて速い** — RustとGPU描画で、サイズは17MB。すぐに起動します。

### 01 · AI Native — エージェントの状態をターミナルが把握します

Claude CodeとCodexから状態イベントを受け取り、応答が終わったとき、権限を求めているとき、回答を待っているときを検知します。該当ペインの枠が光り、ワークスペースにバッジが付き、デスクトップ通知が届きます。通知をクリックするとそのペインに移動します。

- エージェントごとに作業中・完了・待機・権限リクエストを表示
- 確認するまで強調枠は消えません
- ペインごとにモデル、コンテキスト使用量、利用上限、ブランチを表示
- 実行中のサブエージェントと直近の作業内容を確認
- プロジェクトに`cd`すると、前回のセッションを再開するか提案

### 02 · 並列作業 — ひとつのウィンドウで、好きなだけエージェントを

ペインやエージェントの数に制限はありません。タブを分割して、ペインごとに別のエージェントを起動するだけです。たとえばOpusがメインの変更を進める間に、Codexがテストを書き、Geminiがドキュメントを更新できます。ワークスペースはタブ、ペイン、フォルダの構成を覚えているので、再起動してもそのまま戻ります。

- **ワークスペース、タブ、分割** — グループ化、ドラッグ、名前の変更ができ、レイアウトも復元されます。
- **複数ウィンドウ** — ウィンドウごとにレイアウトとミニモードを持てます。
- **開発サーバーのポート** — 待ち受け中のポートがワークスペースの下に表示されます。
- **エージェントが使えるブラウザ** — 内蔵MCPサーバーで、Claude CodeとCodexがクリック、入力、スクリーンショットを行えます。設定でオフにできます。

### 03 · セッションマネージャー — エージェント同士でコンテキストを共有

Claude CodeとCodexがPCに保存している公式のセッション記録をそのまま読み込みます。検索や再開はもちろん、Claudeでの会話をCodexに引き継いだり、2つのセッションをつないで一方の作業をもう一方から見えるようにしたりできます。

- **マルチAIモデル通信** — Claude CodeやCodexのセッションから別のセッションへ線をドラッグすると、コンテキストが渡されます。
- **ライブ接続** — 新しいやり取りが発生するたびに自動で転送します。応答のループは防止されます。
- **双方向接続** — 2つのセッションがお互いの内容を見ながら、同じ問題に取り組めます。
- **Claude ⇄ Codex** — 会話を別のエージェントに移し、必要なら元に戻せます。
- **検索** — タイトル、会話内容、フォルダでセッションを探せます（`⇧⌘O`）。
- **再開** — 新しいワークスペースや新しいタブでセッションを続けられます。

### 04 · 可視化 — 気になることは画面ですぐ確認

エージェントの様子を知るために `ps aux | grep claude` を打つ必要はありません。必要な画面はアプリに入っています。

- **AIプロセス** — エージェントとセッションごとのCPU、メモリ、ディスク使用量。MCPサーバーや子プロセスも含み、2秒ごとに更新されます。
- **ミニモード** — ウィンドウを小さなパネルにして常に手前に表示します。エージェントが終わると吹き出しで知らせます。
- **メニューバー** — 週間の使用量、上限リセットまでの時間、実行中のエージェント一覧を確認できます。
- **AI使用量** — コスト、呼び出し数、キャッシュヒット率、トークン、モデル、ツールをローカルの記録から集計します。
- **AgentGit** — Gitを内蔵しているので、見ているセッションのリポジトリをそのまま管理できます。差分の確認、コミット、ブランチ切り替え、マージまでアプリ内で行えます。
- **アプリ内ブラウザ** — ターミナルの横に開発サーバーを開いて、結果をすぐに確認できます。Claude CodeとCodexもMCP経由で同じブラウザを操作しながら開発やテストを進めます。
- **拡張機能とMCP** — スキル、サブエージェント、コマンド、プラグイン、MCPサーバーをひとつの一覧で管理。公式MCPサーバーはワンクリックで追加できます。
- **APIコネクタ** — HTTP APIをMCPツールにしてエージェントに使わせます。

### 05 · ネイティブ — 17MB、起動してすぐ使えます

ElectronもChromiumも同梱していません。Rustで書かれ、[GPUI](https://gpui.rs)でMetalを使って描画し、ターミナル部分には`alacritty_terminal`を使っています。CPUとメモリはエージェントのために残しておきます。

### すでにインストールしているエージェントをそのまま使えます

Claude CodeとCodexは、リアルタイムの状態表示、セッション管理、コンテキスト共有まで最も深く連携しています。Gemini CLI、Antigravity CLI、Amp、OpenCode、Copilot CLI、Cursor Agent、Qwen Code、Droid、Goose、Crush、AiderとローカルのOllamaモデルは自動で検出され、アプリから起動できます。

### 近日公開

- **アイデアモード** — アイデアから開発、サービスのリリースまでをAgenttyの中でひとつにつなぐオールインワンモード。
- **プラグイン** — プラグインでAgenttyに好きな機能を追加できるようになります。
- **Linux・Windows** — macOSに加えて、LinuxとWindowsへの対応を準備しています。
- **オープンソース化** — すべてのソースコードをGitHubで公開します。

### 会話の記録はあなたのPCだけに

セッション一覧、使用量、引き継ぎはPC上の記録ファイルから作られ、Agenttyが会話の内容を外部に送ることはありません。エージェントの状態イベントと引き継ぎファイルには本人のアカウントだけがアクセスできます。自分で追加したAPIやMCPサーバーを除けば、アプリがネットに接続するのはアップデートとサービス状況の確認だけです。エージェントCLI自体はこれまでどおり各サービスと通信します。

- 会話はローカルに · シークレットはキーチェーンに · 本人だけがアクセスできるファイル · オープンソース化予定

### はじめに

- **ダウンロード** — [Releases](https://github.com/empty-user77/agentty-releases/releases/latest) から最新の `.dmg` を入手し、`Agentty.app` を `/アプリケーション` にドラッグするだけです。Appleによる署名・公証済みです。
- **自動アップデート** — 起動時と1時間ごとに新しいバージョンを確認し、検証済みのアップデートをインストールして再起動します。
- **動作環境** — macOS 13 Ventura以降、Apple Silicon (M1/M2/M3/M4)。エージェントタブには `PATH` 上の `claude` または `codex` が必要です。
- **お問い合わせ** — バグ報告やフィードバックは [Issues](https://github.com/empty-user77/agentty-releases/issues) へお寄せください。

---

## 中文 (简体)

### 为 AI Native 时代打造的终端

在一个窗口运行所有 AI 代理，随时查看状态，并在它们之间共享上下文。

Agentty 是一款用于运行 AI 编程代理的原生终端应用，支持 **Claude Code**、**Codex**、Gemini CLI、Amp、OpenCode 等。它会显示每个代理的状态，可以在分屏中运行任意数量的代理，并让会话之间共享上下文。

> 17MB · 0 Electron · 13+ 代理 CLI · 一个窗口管理所有代理

### 为什么选择 Agentty —— 专为运行代理设计的终端

- **了解代理状态** —— 告诉你代理是在工作、已完成，还是在等你。
- **并排运行** —— 工作区、标签页和分屏，让多个代理放在同一个窗口里。
- **会话管理** —— 恢复旧会话，在 Claude Code 和 Codex 之间迁移或连接。
- **可视化查看** —— 进程、用量、Git 和子代理都有各自的页面。
- **小巧快速** —— Rust 编写，GPU 渲染，只有 17MB，打开即用。

### 01 · AI Native —— 终端知道你的代理在做什么

Agentty 接收 Claude Code 和 Codex 的状态事件，能识别代理何时完成回复、请求权限或等待回答。对应的分屏会高亮边框，工作区会显示徽标，同时弹出桌面通知，点击即可跳转到该分屏。

- 每个代理都显示工作中、已完成、等待中或需要权限
- 高亮会一直保留，直到你查看该分屏
- 每个分屏显示模型、上下文用量、速率限制和 Git 分支
- 查看正在运行的子代理及其最近的操作
- `cd` 进入项目时，会提示恢复该目录下的上一个会话

### 02 · 并行 —— 一个窗口，想开多少代理就开多少

分屏和代理的数量没有限制。拆分标签页，在每个分屏里启动不同的代理即可。比如 Opus 负责主要改动，Codex 编写测试，Gemini 更新文档。工作区会记住标签页、分屏和文件夹，重启后一切保持原样。

- **工作区、标签页、分屏** —— 可以分组、拖动、重命名，布局也会恢复。
- **多窗口** —— 每个窗口都有自己的布局和迷你模式。
- **开发服务器端口** —— 正在监听的端口会显示在工作区下方。
- **代理可用的浏览器** —— 内置 MCP 服务器让 Claude Code 和 Codex 可以点击、输入和截图。可在设置中关闭。

### 03 · 会话管理 —— 在代理之间共享上下文

Agentty 直接读取 Claude Code 和 Codex 保存在电脑上的官方会话记录。你可以搜索、恢复会话，把 Claude 里的对话交给 Codex，或者把两个会话连起来，让一方看到另一方的工作内容。

- **多 AI 模型通信** —— 从一个 Claude Code 或 Codex 会话拖一条线到另一个会话，即可传递上下文。
- **实时连接** —— 新的对话轮次会自动转发，并防止互相回复的循环。
- **双向连接** —— 两个会话能看到彼此的内容，两个代理可以一起解决同一个问题。
- **Claude ⇄ Codex** —— 把对话迁移到另一个代理，需要时再迁回来。
- **搜索** —— 按标题、对话内容或文件夹查找会话（`⇧⌘O`）。
- **恢复** —— 在新的工作区或新标签页中继续会话。

### 04 · 可视化 —— 想知道的，都能在界面上看到

不用再敲 `ps aux | grep claude` 来查看代理的情况，需要的页面都已经内置在应用里。

- **AI 进程** —— 按代理和会话显示 CPU、内存和磁盘占用，包括 MCP 服务器和子进程，每 2 秒刷新一次。
- **迷你模式** —— 把窗口缩成始终置顶的小面板。代理完成时会弹出气泡提示。
- **菜单栏** —— 查看每周用量、距离额度重置的时间，以及正在运行的代理。
- **AI 用量** —— 根据本地记录统计费用、调用次数、缓存命中率、Token、模型和工具。
- **AgentGit** —— 内置 Git，可以直接管理当前会话所在的仓库。查看差异、提交、切换分支和合并都能在应用内完成。
- **内置浏览器** —— 在终端旁打开开发服务器，改动立刻可见。Claude Code 和 Codex 也能通过 MCP 操作同一个浏览器，边开发边测试。
- **扩展与 MCP** —— 技能、子代理、命令、插件和 MCP 服务器集中在一个列表中管理。官方 MCP 服务器一键添加。
- **API 连接器** —— 把 HTTP API 变成 MCP 工具供代理使用。

### 05 · 原生 —— 17MB，打开就能用

没有 Electron，也没有捆绑 Chromium。Agentty 使用 Rust 编写，通过 [GPUI](https://gpui.rs) 在 Metal 上渲染，终端部分使用 `alacritty_terminal`。CPU 和内存留给代理使用。

### 直接使用你已经安装的代理

Claude Code 和 Codex 集成最深，支持实时状态、会话管理和上下文共享。Gemini CLI、Antigravity CLI、Amp、OpenCode、Copilot CLI、Cursor Agent、Qwen Code、Droid、Goose、Crush、Aider 以及本地 Ollama 模型会被自动检测，并可在应用中直接启动。

### 即将推出

- **创意模式** —— 从创意到开发再到上线服务，全部在 Agentty 中一站式完成。
- **插件** —— 通过插件为 Agentty 添加你想要的功能。
- **Linux 与 Windows** —— 除 macOS 外，正在准备支持 Linux 和 Windows。
- **开源** —— 在 GitHub 上公开全部源代码。

### 对话记录只保存在你的电脑上

会话列表、用量统计和对话交接都基于电脑上的记录文件生成，Agentty 不会把你的对话内容发送到任何地方。代理状态事件和交接文件只有你的用户账户可以访问。除了你自己添加的 API 和 MCP 服务器，应用联网只用于检查更新和服务状态。代理 CLI 本身仍会照常与各自的服务通信。

- 对话留在本地 · 密钥存于钥匙串 · 仅本人可访问的文件 · 即将开源

### 开始使用

- **下载** —— 在 [Releases](https://github.com/empty-user77/agentty-releases/releases/latest) 页面获取最新 `.dmg`，将 `Agentty.app` 拖入 `/应用程序` 即可。已通过 Apple 签名与公证。
- **自动更新** —— 启动时及每小时检查一次新版本，验证后自动安装并重启。
- **系统要求** —— macOS 13 Ventura 或更高版本，Apple Silicon (M1/M2/M3/M4)。代理标签页需要 `PATH` 中有 `claude` 或 `codex`。
- **反馈** —— 请在 [Issues](https://github.com/empty-user77/agentty-releases/issues) 中提交问题或建议。

---

<p align="center">
  <a href="https://agentty.run">agentty.run</a> ·
  <a href="https://github.com/empty-user77">GitHub</a> ·
  <a href="https://github.com/empty-user77/agentty-releases/releases">Releases</a>
</p>
