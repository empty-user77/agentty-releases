---
title: 플러그인 빠른 시작
description: 패널과 버튼과 프롬프트를 갖춘 Agentty 플러그인을 몇 분 만에 만들기 — AI로, 또는 직접.
---

플러그인은 매니페스트와 프로그램이 들어 있는 폴더입니다. Agentty는 플러그인이 처음 쓰일 때 그 프로그램을 시작하고 stdin/stdout으로 대화합니다. JSON-RPC 2.0이고, 한 줄에 JSON 객체 하나입니다. 어떤 언어로도 작성할 수 있으며, Node.js SDK를 쓰면 몇 줄이면 됩니다.

## AI로 만들기

**플러그인**(퍼즐 아이콘)을 열고, *직접 만들기* 아래에 이름과 하고자 하는 일을 적은 뒤 **Claude Code로 생성하고 빌드**를 누릅니다.

Agentty가 템플릿으로 플러그인을 만들고(SDK, 타입 정의, 개발자 가이드, 지시가 담긴 `CLAUDE.md`·`AGENTS.md`) 그 폴더에서 Claude Code를 엽니다. **AI 프롬프트 복사**를 누르면 같은 프롬프트를 다른 에이전트에서 쓸 수 있습니다.

## 직접 만들기

플러그인 폴더는 이렇게 생겼습니다.

```
~/.agentty/plugins/hello/
├── agentty-plugin.json   매니페스트
├── main.mjs              플러그인 프로그램
└── agentty-plugin.mjs    SDK
```

SDK는 의존성 없는 파일 하나입니다. 플러그인 페이지에서 **개발자 가이드**를 누르면 Agentty가 SDK와 타입 정의, 문서를 `~/.agentty/plugins/.sdk/`에 풀어 놓습니다. 거기서 `agentty-plugin.mjs`를 `main.mjs` 옆으로 복사하세요.

### 매니페스트

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

`id`는 폴더 이름과 같아야 합니다. 나머지는 [매니페스트 레퍼런스](/docs/plugin-manifest)에 있습니다.

### 프로그램

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

세 가지가 일어납니다.

1. `onPanelOpen`이 패널을 트리로 설명합니다. Agentty가 네이티브로 그리므로 웹뷰가 없습니다.
2. `onEvent('explain', …)`은 그 id를 가진 버튼이 눌렸을 때 실행됩니다.
3. `command('hello.explain', …)`은 같은 코드를 명령 팔레트나 페인 바 버튼에서 실행합니다.

### 불러오기

**플러그인 → 새로 고침**을 누르거나 페이지를 다시 엽니다. 플러그인이 설치됨으로 나타나고, 패널 버튼이 탭 영역에, 명령이 팔레트(⇧⌘P)와 에이전트 페인 위에 생깁니다.

> [!WARNING]
> stdout에 직접 쓰지 마세요. `console.log` 금지입니다. stdout은 프로토콜이 쓰는 통로이며, JSON이 아닌 내용은 로그에 남고 무시됩니다. `plugin.log(...)`나 `console.error(...)`를 쓰세요.

## 작업 중의 반복

- 카드의 **재시작**이나 패널 헤더의 ↻가 코드 변경을 반영합니다.
- 카드의 **로그**는 stderr, 프로토콜 오류, 크래시, 종료 코드를 보여줍니다.
- **개발용 폴더 연결…** 은 복사 없이 이용자 폴더(예: Git 체크아웃)에서 플러그인을 실행합니다. 삭제해도 연결만 해제됩니다.
- **Claude Code로 편집**은 플러그인 폴더에서 워크스페이스를 엽니다.

## 쓸모 있게 만들기

`context`는 이용자가 어디에 있는지 알려줍니다.

```js
plugin.onContextChange((context) => {
  plugin.log('focused pane:', context.pane?.kind, context.pane?.status);
});
```

에이전트에게 일을 시키되 어디로 보낼지는 이용자가 정하게 합니다.

```js
await plugin.injectPrompt({
  text: 'Write release notes for the commits since the last tag.',
  title: 'Release notes',
  target: 'ask',      // 보낼 곳… 다이얼로그를 띄웁니다
  agent: 'claude',
  cwd: context.workspace?.cwd,
});
```

페인의 대화를 읽습니다(`session.read` 권한 필요).

```js
const session = await plugin.getSession({ paneId: context.pane.id, maxTurns: 200 });
plugin.log(session.title, session.turns.length, 'turns');
```

> [!TIP]
> 에이전트에게 요약이나 보고서 같은 결과물을 만들게 하려면, 프롬프트에서 지정한 경로에 파일로 쓰라고 하고 그 파일을 감시하세요. Cosmica 플러그인이 세션 요약을 저장하는 방식입니다.

## 다음으로

- [매니페스트 레퍼런스](/docs/plugin-manifest) — 모든 필드
- [Node.js SDK](/docs/plugin-sdk) — 핸들러, 호출, UI 빌더
- [프로토콜](/docs/plugin-protocol) — 다른 언어로 작성할 때
- [권한](/docs/plugin-permissions) — 무엇을 요청하고 무엇을 하지 말아야 하는지
- [배포하기](/docs/plugin-publishing) — 다른 사람에게 공유하기
