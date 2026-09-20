---
title: 매니페스트 레퍼런스
description: agentty-plugin.json의 모든 필드 — 식별 정보, 실행 방식, 권한, 패널, 명령, 아이콘.
---

`agentty-plugin.json`은 플러그인 폴더 최상위에 놓이며, 이 플러그인이 무엇이고 어떻게 시작하며 무엇을 할 수 있고 인터페이스에 무엇을 더하는지 Agentty에 알려줍니다.

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

## 식별 정보

| 필드 | 필수 | 설명 |
|---|---|---|
| `id` | 예 | `a-z`, `0-9`, `-`로 이루어진 2–40자. **폴더 이름과 같아야 합니다.** |
| `name` | 예 | 스토어와 패널 버튼에 표시 |
| `version` | 예 | `major.minor.patch` |
| `description` | | 스토어 카드의 한 줄 설명 |
| `publisher` | | 제작자 |
| `homepage` | | `https://` 여야 합니다 |
| `keywords` | | 스토어 검색용 |
| `links` | | 카드에 버튼으로 표시되는 `{ "label", "url" }` 최대 6개 (프로젝트 사이트, 문서, 소스) |
| `icon` | | 아래 목록의 아이콘 이름 |

## 실행

| 필드 | 기본값 | 설명 |
|---|---|---|
| `main` | 필수 | 플러그인 폴더 기준 진입점 |
| `runtime` | `node` | `node`(로그인 셸 `PATH`의 Node.js 18+), `python`(`python3 <main>`), `executable`(`<main>`을 직접 실행) |
| `apiVersion` | `1` | 작성 기준이 된 플러그인 API 버전 |
| `activationEvents` | `[]` | `["onStartup"]`이면 Agentty와 함께 시작하고, 아니면 처음 사용할 때 시작 |

Agentty는 플러그인 폴더를 작업 디렉터리로 삼아 프로그램을 실행합니다.

## 다른 앱과의 연동

| 필드 | 설명 |
|---|---|
| `requires` | `{ "name", "url", "note" }` — 이 플러그인이 대상으로 하는 앱이나 서비스. 카드가 발견 여부를 표시하고, 없으면 링크를 제공합니다 |
| `detect` | 연동 대상 앱의 경로(`~` 사용 가능). 발견되면 카드가 **추천**으로 표시됩니다 |

## 권한

```json
"permissions": ["prompt.inject", "terminal.write", "session.read", "workspace.read"]
```

| 권한 | 허용되는 일 |
|---|---|
| `prompt.inject` | 프롬프트 전송 |
| `terminal.write` | 열린 페인에 입력 |
| `session.read` | AI 대화 읽기 |
| `workspace.read` | 워크스페이스 목록 조회, 컨텍스트의 폴더·제목 필드 확인 |

쓰는 것만 요청하세요. 목록은 설치 전에 이용자에게 표시되고, 권한 없는 호출은 실패합니다. [플러그인 권한](/docs/plugin-permissions)을 참고하세요.

## 플러그인이 더하는 것

### 패널

```json
"contributes": { "panel": { "title": "Hello", "icon": "sparkles" } }
```

탭 영역에 버튼이 생기고, 터미널 오른쪽에 패널이 붙습니다(폭 360px, 세로 스크롤). 플러그인이 UI 트리로 내용을 채웁니다 — [SDK](/docs/plugin-sdk) 참고.

### 명령

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

| 필드 | 설명 |
|---|---|
| `id` | 플러그인 안에서 고유. SDK가 이 id로 핸들러를 등록합니다 |
| `title` | 팔레트에 표시. 플러그인 이름을 앞에 붙이면 묶여 보입니다 |
| `description` | 선택적 두 번째 줄 |
| `icon` | 페인 바 버튼의 아이콘 이름 |
| `paneBar` | `true`면 Claude Code·Codex 페인 위 상태 바와 분할 페인 헤더에 아이콘 버튼 추가 |
| `when` | 페인 바 버튼을 `agent` 페인, `shell` 페인, `always` 중으로 제한 |
| `palette` | `false`면 명령 팔레트에서 숨김 |

페인 바 명령은 포커스된 페인이 아니라 **버튼을 누른 페인**의 컨텍스트를 받습니다.

## 아이콘

`icon` 필드에는 아래 이름을 씁니다. 그 외의 값은 퍼즐 조각으로 표시됩니다.

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

## 환경 변수

플러그인 프로그램은 다음 환경 변수를 받습니다.

| 변수 | 의미 |
|---|---|
| `AGENTTY_PLUGIN_ID` | 플러그인 id |
| `AGENTTY_PLUGIN_DIR` | 플러그인 폴더 |
| `AGENTTY_PLUGIN_DATA` | 설정과 캐시를 위한 전용 폴더 |
| `AGENTTY_VERSION` | Agentty 버전 |
| `AGENTTY_LANGUAGE` | 이용자 언어 (`en`, `ko`, `ja`, `zh`) |
| `AGENTTY_BIN` | `agentty` 명령줄 도구 경로 |

저장할 것은 모두 `AGENTTY_PLUGIN_DATA` 안에 두세요.
