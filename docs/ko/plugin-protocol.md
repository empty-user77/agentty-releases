---
title: 플러그인 프로토콜
description: SDK 뒤에서 오가는 JSON-RPC 와이어 포맷 — 어떤 언어로든 Agentty 플러그인을 만들기 위한 명세.
---

이 페이지는 와이어 포맷 API **버전 1**을 설명합니다. JavaScript로 작성한다면 [Node.js SDK](/docs/plugin-sdk)가 전부 감싸 주며, 어느 쪽이든 [빠른 시작](/docs/plugin-quickstart)을 먼저 읽는 편이 좋습니다.

## 전송 방식

Agentty는 플러그인 폴더를 작업 디렉터리로 삼아 프로그램을 시작합니다.

| `runtime` | 실행 명령 |
|---|---|
| `node` | `node <main>` — 로그인 셸 `PATH`, Homebrew, Volta, nvm의 Node.js |
| `python` | `python3 <main>` |
| `executable` | `<main>` |

메시지는 [JSON-RPC 2.0](https://www.jsonrpc.org/specification) 객체이며 **한 줄에 하나**, UTF-8로, stdin(Agentty → 플러그인)과 stdout(플러그인 → Agentty)을 통해 오갑니다. 16MB가 넘는 줄은 거부됩니다. stdout의 JSON이 아닌 내용은 로그에 남고 무시되며, stderr는 플러그인 로그로 갑니다.

stdin이 닫히거나 `shutdown`이 오면 종료하세요. `shutdown` 후 1.5초가 지나도 살아 있으면 `SIGTERM`을, 다시 1.5초 뒤에 `SIGKILL`을 받습니다. Agentty가 종료될 때는 둘 다 즉시 이어집니다.

## Agentty → 플러그인

| 메시지 | 종류 | `params` |
|---|---|---|
| `initialize` | 요청 — 응답 필요 | `{ apiVersion, agentty: { version }, plugin: { id, name, version, dir, dataDir }, language, context }` |
| `command/execute` | 알림 | `{ command, args, context }` |
| `panel/open` · `panel/close` | 알림 | `{ context }` |
| `ui/event` | 알림 | `{ element, event, value?, item?, action?, context }` |
| `context/changed` | 알림 | `{ context }` |
| `url/open` | 알림 | `{ path, query, url, context }` |
| `shutdown` | 알림 | `{}` |

`initialize`가 먼저 오고, 곧바로 플러그인을 시작시킨 것(명령, 패널 열기, 링크)이 뒤따릅니다. `initialize`에는 아무 결과나 응답하면 됩니다(예: `{}`).

## 플러그인 → Agentty

결과나 오류를 받으려면 요청(`id` 포함)으로, 필요 없으면 알림(`id` 없음)으로 보냅니다.

| 메서드 | 권한 | `params` | 결과 |
|---|---|---|---|
| `ui/setPanel` | | `{ tree }` | `null` |
| `ui/showPanel` | | `{}` | `null` |
| `ui/notify` | | `{ message, kind }` — `info`, `success`, `warning`, `error` | `null` |
| `ui/setBadge` | | `{ text }`, 최대 8자 | `null` |
| `context/get` | | `{}` | 컨텍스트 |
| `host/info` | | `{}` | `{ version, apiVersion, language }` |
| `host/openUrl` | | `{ url }` — http/https | `null` |
| `host/revealPath` | `workspace.read` | `{ path }` — 존재하는 절대 경로 | `null` |
| `prompt/inject` | `prompt.inject` | `{ text, title?, target?, paneId?, workspaceId?, agent?, cwd?, submit? }` | `{ status: "asked" }` 또는 `{ status: "sent", paneId }` |
| `terminal/send` | `terminal.write` | `{ paneId?, text, submit? }` — `paneId` 없으면 포커스된 페인 | `{ paneId }` |
| `session/get` | `session.read` | `{ paneId?, maxTurns? }` — 기본 200, 최대 2000 | `{ paneId, agent, sessionId, title, cwd, status, turnCount, turns }` |
| `workspace/list` | `workspace.read` | `{}` | `[{ id, name, cwd, active, panes }]` |

700ms보다 빠르게 도착하는 `ui/notify`는 정상 응답 후 폐기되며, 초당 240건을 넘게 보내는 플러그인은 중단됩니다. 컨텍스트 필드는 플러그인의 권한에 따라 제한됩니다.

### 오류 코드

| 코드 | 의미 |
|---|---|
| `-32601` | 알 수 없는 메서드 |
| `-32602` | 잘못된 파라미터 — 잘못된 UI 트리, 존재하지 않는 페인 등 |
| `-32001` | 권한 없음, 또는 링크 도달 후 1분간의 차단 |
| `-32002` | 사용 불가 — 열린 창이 없음, 세션이 아직 없음 |

## 주고받는 예시

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

`→`는 Agentty에서 플러그인으로, `←`는 플러그인에서 Agentty로입니다. 요청 id는 방향별로 셉니다.

## UI 트리

모든 노드는 `type`을 가진 객체입니다.

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

이벤트: `button`은 `click`, `input`은 `value`와 함께 `change`·`submit`, `list`는 `item`과 함께 `select`(행 버튼은 `item`·`action`과 함께 `action`), `choice`는 선택한 값과 함께 `change`, `toggle`은 새 불리언과 함께 `change`를 보냅니다.

리스트 항목의 `tone`은 아이콘 색을 정하며 `badge`와 같은 값을 씁니다.

## Agentty 없이 테스트하기

플러그인은 stdin을 읽고 stdout에 쓰는 평범한 프로그램이므로 테스트에서 직접 구동할 수 있습니다. `initialize` 요청을 쓰고, 확인하고 싶은 알림을 이어서 보낸 뒤, 플러그인이 돌려주는 JSON을 검증하면 됩니다.
