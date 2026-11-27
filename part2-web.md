# 파트 2. 웹 화면 담당

## 한 줄 요약

사용자가 실제로 보고 누르는 모든 화면을 만드는 파트예요.

## 뭘 만드나요?

카카오톡 단톡 + 구글 캘린더를 합친 웹사이트를 떠올리면 돼요.

* 로그인하고 팀(워크스페이스)을 만들거나 초대 링크로 들어가는 화면
* 왼쪽에 채널 목록, 가운데 채팅, 오른쪽에 다가오는 일정이 보이는 메인 화면
* 캘린더 화면, 할 일 목록 화면
* AI가 "10/15(목) 19:00 회의, 캘린더에 추가할까요?"라고 띄우는 카드
* 욕설이 가려진 메시지("가려진 메시지 [보기]")와 신고 버튼
* 설정 화면, AI 성능 그래프 대시보드

## 왜 중요한가요?

AI가 아무리 좋아도 발표에서 심사위원이 보는 건 결국 화면이에요. 시연의 얼굴을 만드는 파트예요.

## 어려운 부분은 누가 해요?

서버, 로그인, 실시간 연결은 이미 다 만들어져 있어요. `useMessages(channelId)`를 부르면 메시지 목록이 실시간으로 나오고, `send("안녕")`을 부르면 전송돼요. 화면을 만드는 것에만 집중하면 돼요. 사용법은 아래 "실제로 시작하기"에 있어요.

## 진행 순서

| 주차 | 할 일 |
|-----|-----|
| 1 | Next.js 튜토리얼, 화면 스케치(종이나 Figma), 로그인 화면 |
| 2 | 워크스페이스 만들기/들어가기, 채널 목록 |
| 3 | 채팅 화면 (더미 데이터로 먼저) |
| 4 | 캘린더, 할 일 목록 |
| 5 | AI 일정 카드, 욕설 가림 UI, 설정 화면 |
| 6 | 신고 버튼, AI 성능 대시보드 |
| 7\~8 | 버그 수정, 시연 리허설 |

## 첫 주에 할 일

- [ ] Next.js 공식 튜토리얼(nextjs.org/learn) 앞부분 따라 하기
- [ ] 팀 레포 [web](https://github.com/ai-class-project-2026/web) 받아서 로컬에서 실행해보기
- [ ] 기획서 7장(화면 구성)을 보고 화면 7개를 손으로 스케치
- [ ] 로그인 화면 하나 완성해서 첫 PR 올리기

## 알아두면 좋은 용어

* **컴포넌트**: 화면 조각. 버튼, 메시지 한 줄, 카드 하나가 다 컴포넌트예요.
* **props**: 컴포넌트에 넘겨주는 값. 예를 들어 `<MessageItem text="안녕" />`
* **더미 데이터**: 진짜 서버 대신 쓰는 가짜 데이터. 이걸로 화면을 먼저 완성하고 나중에 진짜로 바꿔 끼워요.
* **API / WebSocket**: 화면이 FastAPI 서버와 데이터를 주고받는 통로. API는 요청할 때마다 받아오고, WebSocket은 새 메시지를 서버가 바로 밀어줘요. 연결 코드는 전문가가 세팅해줘요.

## 이렇게 되면 끝이에요

* 더미 데이터로 모든 화면이 동작함
* 전문가가 준 훅을 연결하면 실제 채팅, 일정이 화면에 뜸

## 이 파트를 하면 얻는 것

포트폴리오에 바로 보여줄 수 있는 실제로 돌아가는 웹 서비스 화면, 그리고 React/Next.js 실무 감각.

---

# 실제로 시작하기

## 1. 내 컴퓨터에서 띄우기

server와 web 레포를 **같은 폴더 안에 나란히** 받아요.

```sh
git clone https://github.com/ai-class-project-2026/server.git
git clone https://github.com/ai-class-project-2026/web.git
```

서버 (터미널 1). [uv](https://docs.astral.sh/uv/)가 필요해요.

```sh
cd server
cp .env.example .env
python3 -c "import secrets; print(secrets.token_urlsafe(32))"   # 나온 값을 .env의 JWT_SECRET= 뒤에 붙여넣기
uv sync
make migrate
make dev        # http://localhost:8000/docs 에서 모든 API를 직접 눌러볼 수 있어요
```

웹 (터미널 2):

```sh
cd web
cp .env.example .env.local
npm install
npm run dev     # http://localhost:3000
```

## 2. 이미 만들어져 있는 것

| 쓰는 법 | 하는 일 |
|---|---|
| `import { login, signup, logout, useMe } from "@/lib/auth"` | 로그인. `await login(email, pw)` → `{ ok }`. `useMe()`는 로딩 중 `undefined`, 로그아웃 상태 `null`, 로그인 상태면 사용자 |
| `import { api } from "@/lib/api"` | 나머지 모든 API. 주소를 치면 자동완성되고, 필드를 틀리면 빨간 줄이 떠요 |
| `import { useMessages } from "@/lib/ws"` | 채팅: `const { messages, send, loadOlder, hasMore } = useMessages(channelId)` |
| `import { useSuggestions } from "@/lib/ws"` | AI 일정 카드: `const { suggestions, act } = useSuggestions(channelId)` |
| `import { splitSpans } from "@/lib/mask"` | 욕설 가리기: 메시지를 `[{ text, hidden }]` 조각으로 나눠줘요 |
| `import type { Workspace, Message, Event, Todo, Suggestion } from "@/lib/api"` | props 타입 |

API 호출 예시:

```tsx
const { data: workspaces } = await api.GET("/workspaces");
const { data: ws } = await api.POST("/workspaces", { body: { name: "팀플", ai_schedule_on: true } });
await api.POST("/invites/{code}", { params: { path: { code } } });   // 초대 링크로 참가
const { data: members } = await api.GET("/workspaces/{workspace_id}/members", {
  params: { path: { workspace_id } },
});
```

`{ data, error }`에서 `data`가 없으면 실패예요. `error.detail`에 이유가 들어 있어요.

## 3. 화면별 할 일

폴더 구조는 기획서 16.2를 따라요. 아래 경로마다 빈 화면이 이미 있고, 할 일은 파일 맨 위 `TODO(part 2)` 주석에 적혀 있어요. 채팅 부품은 `components/chat/`(MessageList, MessageInput, MaskedMessage), AI 카드는 `components/schedule-card/ScheduleCard.tsx`예요. 서버 없이 만들 때는 `lib/dummy.ts`의 가짜 메시지·카드·일정·할 일을 넣어 보세요. `npm run dev` 후 http://localhost:3000 첫 화면에 모든 화면 링크가 있어요.

| 화면 | 경로 | 쓰는 것 | 메모 |
|---|---|---|---|
| 로그인/가입 | `app/(auth)/login/` | `login`, `signup` | 성공하면 `/onboarding`이나 워크스페이스로 이동 |
| 온보딩 | `app/onboarding/` | `POST /workspaces` | **대화 분석 동의 체크박스** 값이 `ai_schedule_on` (미성년자 대상이라 필수) |
| 초대 링크 | `app/invite/[code]/` | `POST /invites/{code}` | 링크 = `https://<사이트>/invite/<invite_code>` |
| 채널 목록 | 레이아웃 왼쪽 | `GET/POST /workspaces/{id}/channels` | 워크스페이스를 만들면 `일반` 채널이 자동으로 생겨요 |
| 채팅 | `app/w/[workspaceId]/c/[channelId]/` | `useMessages`, `useSuggestions` | 맨 위로 스크롤하면 `loadOlder()` |
| 캘린더 | `app/w/[workspaceId]/calendar/` | `GET/POST /workspaces/{id}/events`, `PATCH/DELETE /events/{id}` | FullCalendar. `status === "cancelled"`는 숨기거나 회색 |
| 할 일 | `app/w/[workspaceId]/todos/` | `GET/POST /workspaces/{id}/todos`, `PATCH/DELETE /todos/{id}` | 담당자는 멤버 목록에서 고르기 |
| 설정 | `app/w/[workspaceId]/settings/` | `PATCH /workspaces/{id}` | owner만 저장 가능 (아니면 403). 내 역할은 멤버 목록에서 `useMe()`의 id로 찾기 |

### 욕설 가림 메시지

메시지에 `is_profane`, `profanity_spans`가 들어 있어요. 워크스페이스 `filter_mode`에 따라 보여주는 방식이 달라요.

* `mask`: `splitSpans(m.text, m.profanity_spans)`로 나눠서 `hidden` 조각만 `●●`로 바꾸고, [보기]를 누르면 원문 표시
* `warn`: 원문은 보여주고 경고 표시
* `notify_admin`: `mask`처럼 가리고, owner 화면에는 따로 표시

`is_profane`인데 `profanity_spans`가 비어 있으면 (AI 모델이 잡은 경우) 메시지 전체를 가려요.
욕이 너무 많은 메시지도 전체를 가려요: 가린 곳이 3군데 이상이거나, 공백을 뺀 글자의 절반 넘게 가려질 때예요 (`lib/mask.ts`의 `tooProfane`, 2026-11-27).
신고 버튼: `api.POST("/reports", { body: { message_id, type: "false_positive" } })` (멀쩡한데 가려짐) 또는 `"missed"` (욕인데 안 가려짐).

### AI 일정 카드

```ts
s.payload.kind    // "add" | "update" | "cancel"
s.payload.event   // { title, date: "2026-10-15", weekday, start: "19:00:00" | null, time_text, location, attendees, absent, note }
```

* `"add"`: "[일정] 10/15(목) 19:00 · 디스코드 · 팀 회의 / 참석: …" + [캘린더에 추가] [수정] [무시]
* `"update"`: "기존 일정을 이렇게 바꿀까요?" / `"cancel"`: "10/15 회의를 취소할까요?"
* 버튼: `act(s.id, "added")`, `act(s.id, "ignored")`, 수정은 폼에서 고친 값으로 `act(s.id, "edited", 고친값)`
* 누가 버튼을 누르면 다른 팀원 화면의 카드도 자동으로 사라져요
* AI 카드는 서버에 일정 모델이 연결돼야 실제로 떠요 (파트 1 담당). 그 전에는 더미 데이터로 카드 UI를 만들어 두면 돼요

### 실시간이 되는지 확인

브라우저 창 두 개(하나는 시크릿 창)에서 다른 계정으로 로그인하고, 같은 채널에서 메시지를 보내 보세요.

## 4. 작업 규칙

* `main`에서 바로 작업하지 말고 브랜치를 따서 작게 PR을 올려요 (`feat/login-page`처럼)
* PR 올리기 전에 `npm run lint && npm run build` 통과 확인
* 서버 API가 바뀌면 `(cd ../server && git pull && make openapi) && npm run gen:api`로 타입을 다시 만들어요
* 하루 이상 막히면 바로 물어보기
