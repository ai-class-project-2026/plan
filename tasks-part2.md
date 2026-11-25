# 파트 2 (웹) 할 일

[전체 정리](handoff-2026-11-25.md)에서 이어지는 문서예요. 1.1\~1.7, 5\~7장은 전체 정리의 번호이고, 2-N은 [파트 2](tasks-part2.md), 3-N은 [파트 3](tasks-part3.md), 4-N은 [파트 4](tasks-part4.md)의 번호예요.

이 장은 web 레포 `3b7112f`(main), server 레포 `4c583f2`(main) 코드를 읽고, 웹을 빌드해서 화면 없는 크롬으로 돌려 본 뒤 썼어요. 줄 번호는 이 커밋 기준이라, 앞 작업에서 코드를 고치면 조금씩 밀려요. 그럴 때는 적어 둔 글자를 검색해서 찾아요.

명령은 **`server`와 `web` 폴더가 나란히 있는 바깥 폴더에서 시작하는 것**으로 적었어요. 이미 그 레포 안이면 `cd` 부분은 빼요.

## 할 일 순서표

| 번호 | 할 일 | 먼저 필요한 것 | 끝나면 누구에게 |
|---|---|---|---|
| 2-1 | 최신 코드 받고 시험 환경 만들기 | 없음 | 혼자 확인 |
| 2-2 | 그동안 바뀐 내 파일 읽고 설명하기 | 2-1 | 파트 1 (질문 목록) |
| 2-3 | 영어로 나오는 오류 문구와 글자 수 제한 고치기 | 2-1 | 파트 1 (PR 리뷰) |
| 2-4 | AI 일정 카드와 채팅 스크롤 고치기 | 2-1 | 파트 1 (PR 리뷰) |
| 2-5 | 로그인, 로그아웃, 서버 오류 때의 구멍 막기 | 2-3 | 파트 1 (PR 리뷰, `lib/` 요청) |
| 2-6 | 동의 문구를 실제 서버 동작과 맞추기 | 2-1, 파트 1·3의 결정 | 파트 1, 3 |
| 2-7 | 막힌 화면과 개발용 글자 정리하기 | 파트 1의 결정 | 파트 1 |
| 2-8 | 욕설 가림, 필터 방식, 신고 버튼 확인하기 | 2-1 | 파트 3, 1 |
| 2-9 | AI 성능 대시보드를 진짜 점수로 바꾸기 | 코드는 2-1, 진짜 점수는 파트 3·4의 CSV | 파트 3, 4 |
| 2-10 | 실제 폰과 브라우저에서 끝까지 해 보기 | 2-3 \~ 2-8 머지 | 모두 |
| 2-11 | 서버 API가 바뀌면 타입 다시 만들기 | 파트 1의 알림 | 파트 1 |
| 2-12 | 시연 리허설 | 2-6, 2-7, 2-9, 2-10, 모델 연결 | 모두 |

2-6과 2-7은 다른 사람의 결정을 기다려야 해요. 2-1을 끝내자마자 질문부터 보내 두고, 기다리는 동안 2-2 \~ 2-5를 해요.

## 이미 된 것 (다시 하지 않아도 돼요)

* `app/`, `components/`에 `TODO(part 2)`는 하나도 안 남았어요. 남은 `TODO`는 전부 `TODO(part 1)`이에요 (`components/app/MyPage.tsx`, `components/dm/DMProvider.tsx`)
* 기획서 7장의 화면 7개가 모두 있고 빌드돼요. 서버 없이 보는 데모 주소도 있어요 (`/w/demo`, `/dm/demo`, `/me/demo`)
* 보내기 버튼을 두 번 눌러도 메시지는 한 번만 저장돼요 (돌려서 확인)
* 네트워크가 끊긴 채 보내면 `전송하지 못했어요. 서버 연결을 확인해 주세요`가 뜨고 쓴 글이 남아요. 다시 연결하고 보내면 한 개만 저장돼요 (돌려서 확인)
* 서버를 껐다 켠 사이에 쌓인 메시지 60개가 다시 연결된 뒤 모두 채워졌어요 (돌려서 확인)
* 토큰이 잘못됐을 때(401) 메시지를 보내면 `/login?next=…`로 가요 (돌려서 확인)
* 팀원(방장 아님)의 설정 화면은 잠기고 [저장하기]가 없어요 (돌려서 확인)
* 이름 50자, 할 일 200자, 일정 제목 100자, 아주 긴 링크를 넣어도 폭 360, 390, 820, 1440에서 화면이 옆으로 밀리지 않았어요 (돌려서 확인). 진짜 폰에서는 아직이에요 (2-10)
* 초대 화면(`app/invite/[code]/page.tsx`)은 코드를 먼저 확인하고, AI가 켜진 팀과 꺼진 팀에 다른 안내를 보여줘요
* `lib/api/schema.d.ts`는 지금 서버 코드로 새로 만든 것과 똑같아요. 지금은 다시 만들 필요가 없어요 (2-11)
* `npm run lint`, `npm run build`, `npm test`(6개)가 통과해요

## 모든 작업에서 지킬 것

1. **내 폴더만 고쳐요.** `app/`, `components/`, `lib/dummy.ts`가 파트 2예요. `lib/api/`, `lib/auth/`, `lib/ws/`는 파트 1이라 고치지 말고 알려요. 나머지(`lib/mask.ts`, `package.json` 등)는 먼저 물어봐요 (`AGENTS.md`). 예외는 두 가지예요: 명령으로 다시 만드는 `lib/api/schema.d.ts`(2-11)와 점수 CSV를 복사하는 `public/reports/`(2-9).
2. **main에서 바로 작업하지 않아요.** 작업마다 브랜치를 새로 만들어요.

```sh
cd web
git switch main && git pull
git switch -c fix/error-messages        # 작업에 맞는 이름으로
```

3. **PR은 작게.** 아래 2-N 하나가 PR 하나예요. 2-5처럼 여러 가지면 더 나눠도 좋아요. 올릴 때는 내가 고친 파일과 AI 기록만 골라서 올려요.

```sh
git status --short                       # 바뀐 파일 보기
git add app components ai-sessions/part2 # 작업과 상관없는 파일은 빼요
git commit -m "Show API errors in Korean" # 무엇을 바꿨는지 한 줄
git push -u origin fix/error-messages    # 그다음 GitHub에서 PR을 만들고 파트 1을 리뷰어로
```

4. **PR 올리기 전에** `npm run lint && npm run build && npm test`가 전부 통과해야 해요.
5. **AI를 썼으면 기록도 같은 PR에 넣어요** (기획서 17.8). Claude Code는 `git config ai.part part2`를 한 번 해 두면 `ai-sessions/part2/`에 자동으로 쌓여요. ChatGPT 웹 같은 다른 도구는 `ai-sessions/README.md` 양식대로 `ai-sessions/part2/날짜_도구_주제.md`에 직접 붙여 넣어요. 비밀번호, `.env`, 토큰은 넣지 않아요.
6. **내가 올린 코드는 내가 설명해요** (17.8 규칙 5). 아래 코드 조각은 web을 복사한 폴더에 넣고 lint, build, 화면 없는 크롬까지 돌려 본 거예요. 그대로 붙여 넣더라도 한 줄씩 뜻을 말할 수 있어야 해요.
7. **새 라이브러리를 설치하지 않아요** (`AGENTS.md`). 필요하면 먼저 물어봐요.
8. **하루 이상 막히면 바로 공유해요** (기획서 17.7). "무엇을 했고, 무엇이 나왔고, 무엇을 기대했는지"와 화면 사진을 같이 보내요.

---

## 2-1. 최신 코드 받고 시험 환경 만들기

**왜**: 뒤의 모든 확인은 진짜 서버와 계정 두 개가 있어야 할 수 있어요. 예전에 `make seed`로 만든 시연 팀에는 함정이 두 개 있어서 먼저 알고 시작해요 (새로 만들면 없어요).

**준비물**: server, web 레포가 같은 폴더에 나란히 있어야 해요. uv, Node 22 이상, 크롬.

**순서**

1. 두 레포를 최신으로 받아요. `server/.env`나 `web/.env.local`이 없으면 먼저 `plan/part2-web.md`의 "내 컴퓨터에서 띄우기"를 해요 (`.env`가 없으면 `make migrate`가 `jwt_secret Field required`로 멈춰요).

```sh
cd server && git switch main && git pull && uv sync && make migrate
cd ../web && git switch main && git pull && npm install
git config ai.part part2          # 이 레포에서 처음 한 번만
git config ai.part                # part2 라고 나오면 성공
```

2. 서버를 켜요 (터미널 1).

```sh
cd server
make seed      # 이미 했으면 "already seeded"라고 나와요. 괜찮아요
make dev       # 이 터미널은 그대로 둬요
```

3. 웹을 켜요 (터미널 2).

```sh
cd web
cat .env.local        # NEXT_PUBLIC_API_URL=http://localhost:8000 인지 확인
npm run dev
```

4. 계정 두 개로 들어가요. 크롬 일반 창은 `minjun@demo.com`(방장), 시크릿 창은 `seoyeon@demo.com`(팀원). 비밀번호는 둘 다 `demo1234`예요. 같은 브라우저의 다른 탭은 로그인이 공유되니까 꼭 시크릿 창이나 다른 브라우저를 써요.
5. **옛날에 `make seed`를 했다면 함정 1: AI 카드가 안 떠요.** 예전 seed는 AI를 켠 시각(`ai_enabled_at`)을 비워 놨어요. server `76647aa`부터는 고쳐졌어요. 이미 만든 DB라면 민준으로 [설정] → AI 끄기 → [저장하기] → 다시 켜기 → [저장하기]를 해요.
6. **같은 경우 함정 2: 시연 팀의 초대 링크가 데모 화면으로 가요.** 예전 초대 코드가 `demo`였어요. 새로 만든 DB는 `demo-team`이라 괜찮아요. 옛날 DB라면 초대는 **새로 만든 팀**으로 확인해요.
7. 초대 확인용 팀을 하나 만들어요. 민준으로 왼쪽 **+** → 팀 이름 → [만들기] → [팀원 초대 링크 복사] → 시크릿 창(서연)에서 그 주소를 열어 [동의하고 참가].
8. AI 카드 시험 방법을 하나 골라요.
   * 진짜로: 터미널 1의 서버를 끄고 server README의 Ollama 방법으로 다시 켜요 (`ollama pull qwen2.5:3b` 뒤 `SCHEDULE_LLM_URL=http://localhost:11434 SCHEDULE_LLM_MODEL=qwen2.5:3b make dev`). 학습 전 모델이라 카드 내용이 틀릴 수 있어요. 카드 모양만 봐요.
   * 모델 없이: 아래 "시험 카드 넣기"를 써요.

**시험 카드 넣기 (내 컴퓨터의 시험 DB에만)**: `server` 폴더에서 실행해요. `CH`는 채널 번호(주소 `/w/1/c/1`의 마지막 숫자), `EV`는 **같은 팀의** 일정 번호예요. 시연 팀(팀 번호 1)에는 일정이 두 개 있어요. 새로 만든 팀에는 일정이 없으니, 거기서 하려면 캘린더에서 일정을 먼저 하나 만들어요. 다른 팀의 일정 번호를 넣으면 변경, 취소 카드를 눌렀을 때 `이 일정은 이미 지워졌어요`가 나와요.

```sh
# 일정 번호 보기 (번호, 팀 번호, 제목)
uv run python -c "import sqlite3; print(sqlite3.connect('app.db').execute('select id, workspace_id, title from events').fetchall())"

uv run python - <<'PY'
import sqlite3, json, datetime
CH, EV = 1, 1        # 내 채널 번호, 같은 팀의 일정 번호로 바꿔요
def card(kind, title, event_id=None):
    event = {"title": title, "date": "2026-12-03", "weekday": "목", "start": "19:00:00", "status": "confirmed", "attendees": ["테스트"]}
    payload = {"kind": kind, "event_id": event_id, "message_ids": [], "event": event}
    now = datetime.datetime.now(datetime.timezone.utc).strftime("%Y-%m-%d %H:%M:%S")
    db.execute("insert into ai_suggestions (channel_id, payload_json, model_version, created_at) values (?, ?, 'test', ?)", (CH, json.dumps(payload), now))
db = sqlite3.connect("app.db")
card("add", "시험 카드")
card("update", "시험 변경", EV)
card("cancel", "시험 취소", EV)
db.commit()
PY
```

넣은 뒤 그 채널의 채팅 화면을 **새로고침**하면 카드 세 장이 보여요. 다 눌러서 없어지면 다시 넣어요. `server/app.db`는 git에 올라가지 않아요. 이 파일을 다른 팀원과 주고받지 않아요.

**확인할 것**

| 해 보기 | 나와야 하는 것 |
|---|---|
| 민준이 `안녕` 보내기 | 서연 창에 새로고침 없이 바로 나타남 |
| 새 팀 초대 링크를 서연이 열기 | 팀 이름과 안내가 있는 "팀에 초대받았어요" 화면 |
| `http://localhost:3000/invite/nope` | `초대 링크가 올바르지 않거나 만료됐어요` |
| 시험 카드 넣고 새로고침 | 카드 3장 (새 일정, 일정 변경, 일정 취소) |
| 서버를 `Ctrl+C`로 끄고 새로고침 | `서버에 연결할 수 없어요. 서버가 켜져 있는지 확인해 주세요`와 [다시 시도] |

**끝 기준**: 위 표 다섯 줄이 모두 되고, `git config ai.part`가 `part2`예요.

**넘길 것**: 안 된 것이 있으면 파트 1에게 알려요. 함정 두 가지는 파트 1이 고쳤어요 (server `76647aa`).

**막히면**: 로그인이 `서버에 연결할 수 없어요`면 터미널 1에 빨간 오류가 있는지, `.env`의 `JWT_SECRET`이 32자 이상인지 봐요. Windows에서 `make`가 없거나 `<<'PY'`가 안 되면 Git Bash나 WSL을 쓰고, 그래도 안 되면 파트 1에게 물어봐요.

---

## 2-2. 그동안 바뀐 내 파일 읽고 설명하기

**왜**: 내 담당 파일인데 내가 설명하지 못하면 안 돼요 (기획서 17.8 규칙 5). 파트 1이 `a93fbd4`에서 화면 파일을 많이 고쳤고, 그 뒤 커밋 6개가 `app/`, `components/` 파일 38개를 더 바꿨어요(화면 모양과 터치 도구 위주).

**준비물**: 2-1의 web 레포.

**순서**

1. 커밋을 오래된 것부터 하나씩 봐요. 한 번에 다 보지 말고 하나씩 봐요.

```sh
cd web
git show a93fbd4 -- app components     # 재연결, 재시도, 카드 상태, 초대 미리보기
git show e1d2628 -- app components     # 움직임, 아바타, 일정 카드 모양
git show 2bfcbef -- app components     # 글꼴을 IBM Plex Sans KR 하나로
git show d87eb5c -- app components     # 아이콘을 전부 선 모양으로
git show 5c71a9f -- app components     # 화면별 다듬기 (제일 커요)
git show aa84190 -- app components     # 터치 화면에서 메시지 도구 보이기
git show 3b7112f -- app components     # 아바타를 boring-avatars로
```

화면을 넘길 때는 스페이스, 나갈 때는 `q`를 눌러요.

2. 아래 질문에 내 말로 답을 적어요. 모르는 것은 "모름"이라고 적고 파트 1에게 물어봐요.

| 질문 | 볼 파일 | 내 답 |
|---|---|---|
| 메시지 전송이 실패하면 쓴 글은 어디에 남나요? `drafts`와 `attempts`는 왜 두 개인가요? | `components/chat/MessageInput.tsx` | |
| 같은 카드를 두 사람이 동시에 누르면 어떻게 되나요? (409) | `app/w/[workspaceId]/c/[channelId]/page.tsx` 66줄 근처 | |
| 내가 등록한 카드는 "등록됨"으로 남고, 팀원 화면의 카드는 왜 사라지나요? | `components/schedule-card/ScheduleCard.tsx`, `lib/ws/index.ts` 337줄 근처 | |
| 일정을 고칠 때 왜 바뀐 칸만 보내나요? | `components/calendar/EventDialog.tsx`의 `changes` | |
| `is_profane`인데 `profanity_spans`가 이상하면 왜 전체를 가리나요? | `components/chat/MaskedMessage.tsx` | |
| 터치 화면에서 메시지 도구는 어떻게 나타나나요? | `components/chat/MessageList.tsx` 198줄, 218줄 | |
| 체크박스를 빠르게 여러 번 누르면 어떤 답을 믿나요? | `components/workspace/WorkspaceProvider.tsx`의 `updateTodo` | |
| 초대 화면에서 팀 이름은 어느 API에서 오나요? | `app/invite/[code]/page.tsx` | |

3. 1.1 표의 API 변화가 화면에 들어가 있는지 짝을 맞춰요.

| 서버 변화 | 화면에서 볼 곳 | 확인 결과 |
|---|---|---|
| `client_id`로 두 번 저장 막기 | `MessageInput.tsx`의 `newClientId()` | |
| 신고는 한 번만 | `MaskedMessage.tsx`, `MessageList.tsx`의 `reporting` | |
| 413, 글자 수 제한 | 영어로 나와요 → 2-3에서 고쳐요 | |
| 초대 미리보기 `GET /invites/{code}` | `app/invite/[code]/page.tsx` 42줄 | |
| AI 켠 시각 이후만 분석 | 문구가 화면마다 달라요 → 2-6에서 고쳐요 | |

**확인할 것**: `npm run lint && npm run build && npm test`가 통과해야 해요. 그리고 표의 답을 보지 않고 파트 1 앞에서 말로 설명해 봐요.

**끝 기준**: 질문 8개에 모두 답이 있고, "모름"은 파트 1에게 물어서 채웠어요.

**넘길 것**: 질문 목록과 이상한 점을 파트 1에게. 고칠 게 있으면 작은 PR로.

**막히면**: 커밋 하나에서 30분 넘게 막히면 그 파일만 열고 파일 맨 위 주석부터 읽어요. 거의 모든 파일에 "이 파일이 하는 일"이 한국어로 적혀 있어요.

---

## 2-3. 영어로 나오는 오류 문구와 글자 수 제한 고치기

**왜**: 서버는 오류를 영어로 보내고, 웹은 몇 가지만 한국어로 바꿔요 (`components/ui/format.ts`의 `KO_DETAIL`). 나머지는 영어가 그대로 화면에 떠요. 실제로 돌려 보니 이랬어요.

| 해 본 것 | 지금 화면에 나오는 말 |
|---|---|
| 이미 있는 이메일로 가입 | `Email already registered` |
| 이름에 스페이스만 넣고 가입 | `String should have at least 1 character` |
| 이름 51자로 가입 | `String should have at most 50 characters` |
| 채팅에 2,001자 보내기 | `String should have at most 2000 characters` |
| 채팅에 약 1.1MB 붙여넣고 보내기 (413) | `Request body too large` |
| `/w/abc`처럼 이상한 주소 | `Input should be a valid integer, unable to parse string as an integer` |
| 다른 탭에서 로그아웃한 뒤 보내기 | `Not authenticated` |

**준비물**: 2-1 환경. 브랜치 `fix/error-messages`.

**순서**

1. `components/ui/format.ts`를 열고 `KO_DETAIL`에 다섯 줄을 더해요.

```ts
  "Email already registered": "이미 가입한 이메일이에요. 로그인해 주세요",
  "Request body too large": "내용이 너무 길어요. 줄여서 다시 보내 주세요",
  "Not authenticated": "로그인이 풀렸어요. 다시 로그인해 주세요",
  "Owner only": "방장만 할 수 있어요",
  "Assignee is not a member": "담당자가 이 프로젝트의 멤버가 아니에요",
```

2. 같은 파일의 `errorText`에서 `return detail;`부터 함수 끝까지를 이렇게 바꿔요. 모르는 영어는 기본 문구로, 글자 수 초과는 숫자만 꺼내서 알려 줘요.

```ts
    return /[가-힣]/.test(detail) ? detail : fallback; // 모르는 영어 안내는 기본 문구로
  }
  // 422: 서버가 칸마다 이유를 배열로 보내요. 글자 수 초과만 숫자를 꺼내 알려 줘요
  const max = /at most (\d+) characters/.exec(Array.isArray(detail) ? String(detail[0]?.msg) : "")?.[1];
  return max ? `${max}자까지만 쓸 수 있어요` : fallback;
}
```

3. 입력칸에 `maxLength`를 넣어요. 서버 제한은 `server/backend/app/schemas.py`에 있어요. 이미 있는 것은 팀 이름(`maxLength={40}`)뿐이에요.

| 파일 | 칸 | 넣을 값 |
|---|---|---|
| `app/(auth)/login/page.tsx` 69줄 | 이름 | `maxLength={50}` |
| `app/(auth)/login/page.tsx` 76줄 | 비밀번호 | `maxLength={128}` |
| `components/workspace/WorkspaceShell.tsx` 130줄 | 새 채널 이름 | `maxLength={50}` |
| `app/w/[workspaceId]/todos/page.tsx` 79줄 | 새 할 일 | `maxLength={200}` |
| `components/calendar/EventDialog.tsx` 132, 138, 164줄 | 제목, 장소, 메모 | `100`, `200`, `1000` |
| `components/schedule-card/ScheduleCard.tsx` 166, 169, 172줄 | 카드 수정의 제목, 장소, 메모 | `100`, `200`, `1000` |

   예: `<Input value={name} maxLength={50} onChange=… />`. 채팅 입력창(`MessageInput.tsx`)에는 `maxLength`를 넣지 않아요. 붙여 넣은 글이 말없이 잘리는 것보다, 안내를 보여주고 글을 그대로 남기는 지금 방식이 나아요.

4. 스페이스만 넣은 이름을 막아요. `app/(auth)/login/page.tsx`의 `submit`에서 `submitting.current = true;` **앞**에 넣어요. `app/onboarding/page.tsx`의 `create`에도 같은 방법으로 팀 이름을 막아요 (`setError("팀 이름을 적어 주세요")`).

```ts
    if (isSignup && !name.trim()) return setError("이름을 적어 주세요");
```

5. `npm run lint && npm run build && npm test` 후 PR을 올려요. PR에 아래 확인 표를 채워서 붙여요.

**확인할 것**

| 입력 | 나와야 하는 것 | 결과 |
|---|---|---|
| 이미 있는 이메일로 가입 | `이미 가입한 이메일이에요. 로그인해 주세요` | |
| 이름 칸에 글자 60개 붙여넣기 | 50자에서 멈춤 | |
| 이름에 스페이스만 | `이름을 적어 주세요` | |
| 채팅에 2,001자 (아래 방법) | `2000자까지만 쓸 수 있어요`, 글은 입력창에 남음 | |
| 채팅에 1,100,000자 | `내용이 너무 길어요. 줄여서 다시 보내 주세요`, 글은 남음 | |
| `/w/abc` 열기 | `이 프로젝트에 들어갈 수 없어요` | |
| 비밀번호 7자로 가입 | 브라우저가 막음 (서버로 안 감) | |
| 이메일 `b@localhost`로 가입 | 영어가 아닌 안내 | |
| 서버를 끄고 가입 | `서버에 연결할 수 없어요. 서버가 켜져 있는지 확인해 주세요` | |

긴 글 만드는 법: 채팅 화면에서 F12 → Console에 `copy('가'.repeat(2001))`을 치고 Enter, 입력창에 붙여넣기. 1.1MB는 `copy('a'.repeat(1100000))`.

**끝 기준**: 표의 결과 칸이 모두 맞고, 화면 어디에도 영어 오류가 안 나와요.

**넘길 것**: PR. 파트 1에게 리뷰를 요청해요.

**막히면**: 아직 영어가 나오면 F12 → Network에서 빨간 요청을 눌러 Response의 `detail` 글자를 복사해요. 그 글자가 `KO_DETAIL`의 글자와 한 글자라도 다르면 안 바뀌어요.

---

## 2-4. AI 일정 카드와 채팅 스크롤 고치기

**왜**: 시연의 핵심인 카드에서 세 가지가 이상해요. 모두 돌려서 확인했어요.

1. 카드 [수정]에서 날짜를 12/3(목) → 12/4로 바꿔 등록하면, 왼쪽 달력 칸은 "금"인데 글자는 `12/4(목)`으로 나와요. AI가 준 요일을 그대로 쓰고 있어서예요.
2. 일정 **취소** 카드를 처리해도 `등록됨`이라고 나와요.
3. 옛 대화를 읽는 중인데 팀원이 다른 카드를 [무시]하면, 화면이 맨 아래로 튀어요 (스크롤 300 → 맨 아래).

**준비물**: 2-1의 시험 카드 3장. 브랜치 `fix/schedule-card`.

**순서**

1. `components/schedule-card/ScheduleCard.tsx`에서 `shortDate(event.date, event.weekday)` 두 곳(54줄, 89줄)을 `shortDate(event.date)`로 바꿔요. 요일을 날짜에서 계산하게 돼요.
2. 같은 파일 65줄의 `등록됨`을 `{kind === "cancel" ? "취소됨" : "등록됨"}`으로 바꿔요.
3. `components/chat/MessageList.tsx`의 `// 카드가 새로 뜨면 보이도록 아래로` 아래 `useEffect`를 이렇게 바꿔요. 카드가 **늘었고**, 맨 아래 근처를 보고 있을 때만 따라 내려가요.

```ts
  const cardCount = useRef(0);
  useEffect(() => {
    const el = box.current;
    if (!el) return;
    const nearBottom = prev.current.height - el.scrollTop - el.clientHeight < 160;
    if (cards.length > cardCount.current && nearBottom) el.scrollTo({ top: el.scrollHeight, behavior: "smooth" });
    cardCount.current = cards.length;
    prev.current.height = el.scrollHeight;
  }, [cards.length]);
```

4. `npm run lint && npm run build` 후 PR. 고치기 전/후 화면 사진을 같이 올려요.

**확인할 것**

| 해 보기 | 나와야 하는 것 | 결과 |
|---|---|---|
| "시험 카드" [수정] → 날짜 하루 뒤 → [고친 내용으로 추가] | 달력 칸과 글자의 요일이 같음, `등록됨` | |
| "시험 변경" → [이렇게 바꾸기] (취소보다 먼저 해요) | 원래 일정이 바뀌고 `등록됨` | |
| "시험 취소" → [일정 취소하기] | `취소됨`, 캘린더에서 사라짐 ([취소된 일정도 보기]를 켜면 회색으로 보임) | |
| [캘린더에 추가]를 빠르게 두 번 누르기 | 일정이 하나만 생김 | |
| 두 창에서 같은 카드를 거의 동시에 누르기 | 한쪽만 등록, 다른 쪽은 카드가 조용히 사라짐, 일정은 하나 | |
| 카드 [수정] 중 서버를 끄고 [고친 내용으로 추가] | `서버에 연결할 수 없어요`가 폼 아래에 나오고 고친 값이 남음 | |
| 카드 수정에서 제목을 지우고 저장 | 브라우저가 막음 | |
| 캘린더에서 일정을 새로 만들고, 그 번호로 시험 카드를 넣고, 일정을 [삭제]한 뒤 취소 카드 누르기 | `이 일정은 이미 지워졌어요` | |
| 메시지를 화면 두 배 넘게 쌓고(창 높이를 줄이면 빨라요) 위로 올린 뒤, 다른 창에서 카드 [무시] | 보던 자리 그대로 | |
| 위로 올려 옛 대화를 읽는 중 새 카드가 왔을 때 | 보던 자리 그대로 | |
| 맨 아래에서 새 카드가 왔을 때 | 카드가 보이게 내려감 | |
| 처음 채널을 열었을 때 | 맨 아래부터 보임 | |

"새 카드가 왔을 때" 두 줄은 모델 없이도 볼 수 있어요. 채팅 화면을 켜 둔 채 시험 카드를 넣고, 터미널 1의 서버를 `Ctrl+C`로 껐다가 다시 켜요. 10초쯤 뒤 다시 연결되면서 새로고침 없이 카드가 나타나요.

**끝 기준**: 표가 모두 맞고 PR이 머지됐어요.

**넘길 것**: PR을 파트 1에게. 카드의 요일을 서버에서 온 값 대신 날짜로 계산하게 바꿨다고 적어 줘요.

**막히면**: 카드가 안 보이면 새로고침했는지, `CH`가 주소의 채널 번호와 같은지 봐요. 이미 처리한 카드는 다시 안 나와요. 시험 카드를 다시 넣어요.

---

## 2-5. 로그인, 로그아웃, 서버 오류 때의 구멍 막기

**왜**: 시연에서는 노트북 한 대로 계정을 바꿔 가며 쓰고, 서버가 잠깐 멈출 수도 있어요. 지금 구멍은 이래요.

| 구멍 | 어떻게 확인했나 | 고칠 사람 |
|---|---|---|
| A가 쓰다 만 글을 두고 로그아웃 → B가 로그인하면 같은 채널 입력창에 **A의 글이 보여요** | 돌려서 확인 (`비밀 초안`이 그대로 보임) | 파트 2 |
| 서버 오류(5xx)인데 로그인 화면은 `이메일이나 비밀번호가 맞지 않아요`라고 말해요 | 돌려서 확인 (`app/(auth)/login/page.tsx`의 `setError(isSignup ? …`) | 파트 2 |
| 서버 오류(5xx)면 1:1 대화 화면이 로그인 화면으로 보내 버려요 | 돌려서 확인 (`/login?next=/dm`) | 파트 2 |
| ~~다른 탭에서 로그아웃하면 이 탭은 로그인 화면으로 가지 않아요~~ 고쳤어요 (web `428b543`): 다른 탭에서 로그아웃하거나 계정을 바꾸면 이 탭을 새로 불러와요 | 돌려서 확인 | 파트 1 (끝) |
| 채팅 중 서버가 끊겨도 화면에 "연결이 끊겼어요" 같은 표시가 없어요 | 돌려서 확인 | 파트 1과 정하기 |

**준비물**: 2-1 환경, 2-3 머지. 브랜치 `fix/session`.

**순서**

1. **계정이 바뀔 때 메모리까지 비우기.** 두 곳을 고쳐요. `router.replace` 대신 화면을 새로 읽으면서 이동하면, 메모리에 남은 초안과 프로젝트 목록이 지워져요.
   * `components/app/MyPage.tsx`의 `signOut`

```ts
  const signOut = () => {
    if (demo) return router.replace("/login");
    logout();
    location.replace("/login"); // 새로고침하며 이동: 메모리에 남은 초안·프로젝트 목록까지 지워요
  };
```

   * `app/(auth)/login/page.tsx`의 `router.replace(safeNext(next, location.origin));`을 아래처럼 바꿔요. 로그아웃을 안 누르고 초대 오류 화면의 [다른 계정으로 로그인]으로 바꾸는 경우까지 막아요. 이제 안 쓰는 `const router = useRouter();` 줄과 `import`의 `useRouter`는 지워요.

```ts
        location.replace(safeNext(next, location.origin));
```

2. **로그인 오류 구분하기.** `app/(auth)/login/page.tsx`의 `setError(isSignup ? … )` 줄을 이렇게 바꿔요.

```ts
      setError(
        isSignup ? errorText(error, "가입하지 못했어요. 입력한 내용을 확인해 주세요")
        : (error as { detail?: unknown } | undefined)?.detail === "Wrong email or password" ? "이메일이나 비밀번호가 맞지 않아요"
        : errorText(error, "로그인하지 못했어요. 잠시 후 다시 시도해 주세요"),
      );
```

3. **1:1 대화의 서버 오류.** `components/dm/DMProvider.tsx`에서 `if (!meRes.data) return router.replace("/login?next=/dm");`을 이렇게 바꿔요.

```ts
        if (!meRes.data) {
          if (meRes.response.status === 401) return router.replace("/login?next=/dm");
          setErrorMessage("서버에 문제가 생겼어요. 잠시 후 다시 시도해 주세요");
          return setStatus("error");
        }
```

4. **파트 1에게 요청하기.** 아래 두 가지는 `lib/` 쪽이라 직접 고치지 않아요.
   * 다른 탭 로그아웃은 파트 1이 고쳤어요 (web `428b543`). 새로고침되니 다른 탭에서 쓰다 만 글은 남지 않아요.
   * 채팅 중 연결이 끊긴 상태를 화면에 보여주려면 `useMessages`가 연결 상태를 알려줘야 해요. 필요한지부터 같이 정해요.
5. `npm run lint && npm run build` 후 PR. 세 가지를 PR 세 개로 나눠도 좋아요.

**확인할 것**

| 해 보기 | 나와야 하는 것 | 결과 |
|---|---|---|
| A가 채팅에 글을 쓰다 말고 [내 정보] → [로그아웃] → B로 로그인 → 같은 채널 | 입력창이 비어 있음 | |
| 같은 상황에서 왼쪽 프로젝트 목록 | B의 프로젝트만 보임 | |
| A가 글을 쓰다 말고 왼쪽 **+** → 초대 코드 `nope` → [참가] → [다른 계정으로 로그인] → B로 로그인 → 같은 채널 | 입력창이 비어 있음 | |
| 로그아웃하지 않고 채널 이동 → 다시 돌아옴 | 쓰던 글이 남아 있음 (이건 유지돼야 해요) | |
| 비밀번호 틀리게 로그인 | `이메일이나 비밀번호가 맞지 않아요` | |
| 비밀번호 129자로 로그인 | 2-3의 `maxLength` 때문에 128자에서 멈춤 | |
| 로그인 버튼 빠르게 두 번 | 한 번만 처리 | |
| 서버를 끄고 `/dm` 열기 | `서버에 연결할 수 없어요. 서버가 켜져 있는지 확인해 주세요`, 로그인은 유지 | |
| 가짜 500 서버(아래)를 켜고 `/dm` 열기 | `서버에 문제가 생겼어요. 잠시 후 다시 시도해 주세요`, 로그인 화면으로 안 감 | |
| 가짜 500 서버를 켜고 로그인 | `로그인하지 못했어요. 잠시 후 다시 시도해 주세요` | |
| F12 Console에서 `localStorage.setItem('token','bad')` 후 메시지 보내기 | `/login?next=…`로 이동, 로그인하면 원래 채널로 돌아옴 | |
| 탭 두 개 중 하나에서 로그아웃 → 다른 탭에서 보내기 | 보내기 전에 이미 로그인 화면으로 이동해 있음 (web `428b543` 이후) | |
| `http://localhost:3000/login?next=https://example.com` 에서 로그인 | 다른 사이트로 가지 않음 | |

**가짜 500 서버**: 모든 요청에 500으로 답하는 서버예요. 터미널 1의 진짜 서버를 끄고 그 자리에서 켜요. 끝나면 `Ctrl+C`로 끄고 `make dev`로 되돌려요.

```sh
python3 - <<'PY'
from http.server import BaseHTTPRequestHandler, HTTPServer
class H(BaseHTTPRequestHandler):
    def reply(self):
        self.send_response(200 if self.command == "OPTIONS" else 500)
        self.send_header("Access-Control-Allow-Origin", "*")
        self.send_header("Access-Control-Allow-Headers", "authorization, content-type")
        self.send_header("Access-Control-Allow-Methods", "*")
        self.end_headers()
        if self.command != "OPTIONS":
            self.wfile.write(b'{"detail": "Internal Server Error"}')
    do_GET = do_POST = do_PATCH = do_OPTIONS = reply
HTTPServer(("127.0.0.1", 8000), H).serve_forever()
PY
```

**끝 기준**: 파트 2 구멍 세 개가 막혔고, 파트 1 요청 두 가지를 보냈어요.

**넘길 것**: 파트 1에게 요청 두 가지와 확인 표.

**막히면**: lint가 `'router' is assigned a value but never used`라고 하면 로그인 화면에서 안 쓰는 `router` 줄을 안 지운 거예요. `no-location-assign…` 경고는 `location.replace` 대신 `location.assign`을 쓴 거예요. 가짜 500 서버가 `Address already in use`면 진짜 서버가 아직 켜져 있는 거예요.

---

## 2-6. 동의 문구를 실제 서버 동작과 맞추기

**왜**: 사용자 대부분이 미성년자라서 동의 문구는 실제 동작과 같아야 해요 (기획서 11장). 지금은 화면마다 말이 다르고, 서버 동작과 다른 말도 있어요.

**준비물**: 2-1 환경. 파트 1, 3과 이야기할 시간.

**서버가 실제로 하는 것 (코드로 확인)**

| 서버 동작 | 근거 | 지금 문구 |
|---|---|---|
| AI를 켠 시각 이후의 메시지만 일정 분석에 써요. 껐다 켜면 그때부터 다시예요 | `pipeline.py` 85, 94줄, `workspaces.py` 69줄 | 초대 화면의 "꺼진 팀"에만 있음 |
| 욕설 판정은 AI 동의와 **상관없이** 모든 메시지에 해요. 그 메시지만 보고, 앞 메시지는 쓰지 않아요 (server `bc139d3`) | `chat.py`, `moderation/__init__.py` | 동의 안내에는 없음 (설정의 욕설 필터 설명만 있음) |
| AI 켜고 끄기는 방장만 할 수 있어요 | `workspaces.py` 63줄 | 초대 화면에 있음 |
| AI가 켜져 있는지 새 멤버는 초대 화면에서 봐요. **이미 있던 멤버**는 방장이 나중에 켜도 따로 안내를 받지 않아요 | 안내를 보내는 코드가 없음 | 없음 |
| 신고(`reports`)와 카드 버튼(`ai_suggestions.action`)은 재학습 자료로 쓰려고 저장해요 | `chat.py` 213줄, `calendar.py` 134줄 설명, 기획서 9.7, 10.3 | `AI 학습에 쓰지 않아요` |
| 30일 뒤에 지우는 코드는 없어요 | `server/backend`에서 찾지 못함 | 없음 (기획서 11장에만 있음) |

**문구가 있는 곳**

| 화면 | 파일 | 지금 문구의 문제 |
|---|---|---|
| 팀 만들기 | `components/app/consent.ts`, `app/onboarding/page.tsx` 88줄 | "켠 뒤에 보낸 메시지부터"가 없음 |
| 초대 | `app/invite/[code]/page.tsx` 96 \~ 111줄 | `consent.ts`를 안 쓰고 따로 적혀 있음. `consent.ts` 1줄 주석("똑같은 말을 쓰도록 한곳에")이 사실과 다름 |
| 설정 | `app/w/[workspaceId]/settings/page.tsx` 86줄 | "끄면 대화를 분석하지 않아요" → 욕설 판정은 계속해요 |
| 채팅 위쪽 표시 | `app/w/[workspaceId]/c/[channelId]/page.tsx` 203줄 | `AI 일정 도우미 켜짐`이 폭 640px보다 좁으면 안 보여요. 폰에서는 켜져 있는지 알 수 없어요 (폭 390에서 확인) |

**순서**

1. 아래 결정표를 파트 1, 3에게 보내고 답을 채워요. 혼자 정하지 않아요.

| 정할 것 | 고를 수 있는 답 | 정한 답 (누가) |
|---|---|---|
| 신고한 메시지를 재학습에 쓰나요? | 안 씀 / 동의한 경우만 익명으로 씀 (기획서 11장) | **따로 동의받은 경우만 익명으로 씀** (파트 1, 2026-11-25). 신고할 때 동의를 묻는 화면과 저장할 칸이 아직 없어요. 파트 1이 서버를 만든 뒤 화면을 붙이고, 그 전까지 지금 문구("AI 학습에 쓰지 않아요")를 지켜요. 앞 메시지(문맥)는 그 글을 쓴 사람의 동의가 없으면 넣지 않아요 |
| 카드 버튼 기록을 재학습이나 평가에 쓰나요? | 안 씀 / 씀 (무엇을, 어떻게) | |
| 일정 모델은 어디서 돌아요? "우리 서버 안"이라고 말해도 되나요? | 된다 / 다른 말로 바꾼다 | |
| 욕설 판정도 문구에 넣을까요? | 넣음 / 안 넣음 | |
| 이미 있던 멤버에게는 어떻게 알리나요? | 채팅 위쪽 표시를 폰에서도 보이게 / 팀에서 말로 / 다른 방법 | |
| 30일 보관을 문구에 쓸까요? | 지우는 코드가 생기기 전에는 안 씀 / 파트 1이 만든 뒤 씀 | |

2. 답이 다 오면 `components/app/consent.ts` 한곳에 문구를 모아요. 초대 화면도 여기서 가져오게 바꿔요. 아래는 **초안**이에요. 1번의 답에 맞지 않는 줄은 빼거나 고쳐요.

   * 켜는 팀: "AI 일정 도우미를 켜면, 켠 뒤에 보낸 메시지부터 AI가 약속을 찾아 캘린더 등록을 제안해요."
   * 켜고 끄기: "켜고 끄는 것은 팀 방장(owner)만 할 수 있어요."
   * 욕설: "욕설 가리기는 AI 일정 도우미와 상관없이 모든 메시지에 적용돼요."
   * 학습: (1번 답대로)

3. `app/onboarding/page.tsx`, `app/invite/[code]/page.tsx`, `app/w/[workspaceId]/settings/page.tsx`가 같은 문구를 쓰는지 확인해요. `consent.ts` 첫 줄 주석도 맞게 고쳐요.

```sh
grep -rn "분석" app components     # 남은 옛 문구 찾기
```

4. 채팅 위쪽 표시를 폰에서도 보이게 하기로 했으면, 203줄 다음 줄의 `hidden min-w-0 items-center gap-3 sm:flex`를 `flex shrink-0 items-center gap-3`으로 바꿔요. `shrink-0`이 없으면 채널 이름이 길 때 표시가 `A…`로 줄어요. 폭 360에서 50자 채널 이름으로 확인해요 (이름은 …으로 줄어도 되고, 표시는 다 보여야 해요).
5. `npm run lint && npm run build` 후 PR. 바뀐 화면 4개의 사진을 올려요.

**확인할 것**

| 해 보기 | 나와야 하는 것 | 결과 |
|---|---|---|
| 팀 만들기: 동의 체크 안 하고 만들기 | 팀이 만들어지고 AI 꺼짐 | |
| 팀 만들기: 체크하고 만들기 | AI 켜짐 | |
| AI 꺼진 팀, 켜진 팀 초대 링크 각각 열기 | 각 상황에 맞는 문구, 온보딩·설정과 다른 말이 없음 | |
| 초대 화면에서 [참가하지 않고 나가기] | 참가되지 않음 (서연의 프로젝트 목록에 그 팀이 없음) | |
| [동의하고 참가]를 두 번 빠르게 누르기 | 한 번만 참가, 오류 없음 | |
| 이미 멤버인 사람이 같은 링크 다시 열고 참가 | 오류 없이 그 팀으로 이동 | |
| 틀린 초대 코드 | 동의 화면 없이 `초대 링크가 올바르지 않거나 만료됐어요` | |
| 로그인 안 한 상태로 초대 링크 열기 | 로그인 후 다시 초대 화면으로 돌아옴 | |
| 팀 이름 40자로 만들고 초대 화면 보기 (폭 360) | 글자가 화면 밖으로 안 나감 | |
| 팀원으로 로그인해서 설정 열기 | 잠겨 있음. AI를 켜고 끌 수 없음 | |

**끝 기준**: 결정표가 다 찼고, 온보딩·초대·설정이 같은 내용을 말하고, 그 내용이 위 "서버가 실제로 하는 것" 표와 어긋나지 않아요.

**넘길 것**: 최종 문구와 결정표를 파트 1, 3에게. 파트 3은 3-8(신고 → 재학습)를 이 결정에 맞춰요.

**막히면**: 답이 안 오면 확실한 것(켠 뒤 메시지부터, 방장만, 욕설 판정은 항상)만 먼저 넣은 PR을 올리고, 나머지는 결정표에 "미정"으로 남겨요. 확실하지 않은 약속은 문구에 쓰지 않아요.

---

## 2-7. 막힌 화면과 개발용 글자 정리하기

**왜**: 시연 중에 "준비 중"이나 개발자끼리 쓰는 말이 보이면 안 돼요.

| 화면 | 지금 상태 | 파일 |
|---|---|---|
| 1:1 대화 (진짜 서버) | 보내기가 막혀 있고 `1:1 대화는 서버 준비 중이에요` | `components/dm/DMConversation.tsx`, `DMShell.tsx`, `DMHome.tsx` |
| 1:1로 가는 길 | 맨 왼쪽 첫 아이콘, 멤버 탭의 [대화] 버튼 | `components/app/ProjectRail.tsx`, `components/workspace/MembersPanel.tsx` |
| 비밀번호 변경 | `비밀번호 변경은 서버에 준비 중이에요` | `components/app/MyPage.tsx` |
| 회원 탈퇴 | `회원 탈퇴는 서버에 준비 중이에요` | `components/app/MyPage.tsx` |
| "파트 1"이라는 말 | `서버(파트 1)에 준비 중이에요`, `서버(파트 1)에 비밀번호 변경 기능이 준비되면` | `DMHome.tsx` 45줄, `MyPage.tsx` 168줄 |
| 멘션(@이름) | 기획서 5.1에 P0로 있지만 web, server 어디에도 코드가 없음 | 없음 |
| 첫 화면의 "개발용 화면 바로가기" | 접혀 있지만 누구나 열 수 있음 | `app/page.tsx` 125줄 |
| 설정의 초대 링크 [복사] | 주소가 `http://192.168.…`이면 눌러도 아무 일도 안 일어남 (브라우저가 복사를 막아요) | `app/w/[workspaceId]/settings/page.tsx` 147줄 |
| 일정·멤버 칸 | 창 폭이 1280px보다 좁으면 채팅 오른쪽 일정·멤버 칸과 버튼이 안 보임 | `app/w/[workspaceId]/c/[channelId]/page.tsx` 211, 254줄 |

**준비물**: 파트 1과 결정할 시간. 브랜치 `fix/demo-path`.

**순서**

1. 결정표를 파트 1에게 보내요.

| 정할 것 | 고를 수 있는 답 | 정한 답 |
|---|---|---|
| 1:1 대화 | 파트 1이 API를 만듦 / 시연에서 빼고 들어가는 버튼을 진짜 서버에서 숨김 | |
| 비밀번호 변경 | API를 만듦 / 진짜 서버에서 그 칸을 숨김 | |
| 회원 탈퇴 | API를 만듦 / 진짜 서버에서 그 칸을 숨김 | |
| 멘션 | 만듦 (어디까지) / 기획서에서 우선순위를 내림 | |
| "개발용 화면 바로가기" | 그대로 / 시연 전에 뺌 | |

2. **결정을 기다리지 않아도 되는 것**부터 해요.
   * `DMHome.tsx` 45줄과 `MyPage.tsx` 168줄에서 `(파트 1)`을 지워요.
   * 설정의 [복사]가 실패하면 링크를 보여 줘요. `settings/page.tsx`의 `} catch {}`를 왼쪽 메뉴(`WorkspaceShell.tsx`의 `copyInvite`)와 같은 방법으로 바꿔요.

```ts
                    } catch {
                      prompt("이 링크를 복사해서 팀원에게 보내세요", inviteUrl);
                    }
```

3. 결정이 "숨김"이면 `demo`일 때만 보이게 해요. 화면마다 데모인지 아는 값이 이미 있어요 (`ProjectRail`의 `demo`, `MembersPanel`의 `ws.demo`, `MyPage`의 `demo`). 마이페이지는 칸과 함께 왼쪽 목차(`SECTIONS`)와 `로그아웃 · 회원 탈퇴` 글자도 같이 맞춰요.
4. 결정이 "API를 만듦"이면 파트 1이 API를 올린 뒤 2-11로 타입을 다시 만들고, 코드의 `TODO(part 1)` 자리(`changePassword`, `deleteAccount`, `DMProvider`의 LIVE 부분)에 연결해요. 그 전에는 추측으로 연결하지 않아요.
5. `npm run lint && npm run build` 후 PR.

**확인할 것**

| 해 보기 (진짜 서버) | 나와야 하는 것 | 결과 |
|---|---|---|
| 왼쪽 메뉴, 멤버 탭, 내 정보를 모두 눌러 보기 | 시연 경로에서 "준비 중"이 안 보임 | |
| 주소창에 `/dm/2`를 직접 침 | 정한 대로 (안내 또는 숨김) | |
| 같은 프로젝트가 아닌 사람 번호 `/dm/99999` | `같은 프로젝트에 있는 팀원과만 1:1 대화를 할 수 있어요` | |
| 데모 `/dm/demo-2`, `/me/demo` | 여전히 동작 | |
| 폰에서 설정 → 초대 링크 [복사] | 복사되거나, 링크가 담긴 창이 뜸 | |
| `grep -rn "파트 1" app components` | 화면 글자에는 없음 (주석은 괜찮음) | |

**끝 기준**: 결정표가 다 찼고, 진짜 서버의 시연 경로에서 막힌 화면이 안 나와요.

**넘길 것**: 결정표를 파트 1에게, 멘션 결정은 기획서를 고쳐야 하니 모두에게.

**막히면**: 무엇을 숨겨야 할지 모르겠으면 시연 순서(기획서 15장)를 보고, 그 순서에서 누르는 버튼이 아니면 "숨김" 후보예요.

---

## 2-8. 욕설 가림, 필터 방식, 신고 버튼 확인하기

**왜**: 시연 데모 2("ㅅ1발"은 가려지고 "시발점"은 통과)와 신고 → 재학습 이야기가 이 화면에 달려 있어요. 코드는 있지만 세 가지 방식 × 방장/팀원을 다 눌러 본 기록이 없어요.

**준비물**: 2-1 환경, 계정 두 개. 코드는 `components/chat/MaskedMessage.tsx`, `MessageList.tsx`, `app/w/[workspaceId]/settings/page.tsx`.

**순서**

1. 서연 창에서 아래 문장을 보내고 **민준 창**에서 봐요. 오른쪽 칸은 모델을 연결하지 않았을 때(사전만) 서버가 실제로 준 결과예요.

| 문장 | 모델 없을 때 | 내 화면 |
|---|---|---|
| `ㅅ1발 뭐하냐` | `ㅅ1발`만 검은 줄 | |
| `tlqkf` | 전체 검은 줄 | |
| `시발점` | 안 가려짐 | |
| `꺼져` | 가려짐 (애매한 말은 모델이 없으면 가려요) | |
| `안녕하세요` | 안 가려짐 | |

2. 민준이 설정에서 방식을 바꿔 가며 [저장하기], **두 창을 모두 새로고침**하고 표를 채워요. 방식이 바뀌어도 이미 열려 있는 다른 사람 화면은 새로고침해야 바뀌어요 (돌려서 확인).

| 방식 | 방장 화면에 보여야 하는 것 | 팀원 화면에 보여야 하는 것 | 방장 결과 | 팀원 결과 |
|---|---|---|---|---|
| 가리기 | 검은 줄, `부적절한 표현이 가려졌어요`, [보기], [욕설 아님] | 방장과 같음 | | |
| 경고만 | 원문, `부적절한 표현이 있어요`, [욕설 아님], [보기] 없음 | 방장과 같음 | | |
| 가리기 + 방장 알림 | 가리기와 같고 `관리자 알림` 표시가 더 있음 | `관리자 알림` 없음 | | |

3. 신고 버튼을 눌러 봐요. 서버에 저장됐는지는 이렇게 봐요 (`server` 폴더).

```sh
uv run python -c "import sqlite3; print(sqlite3.connect('app.db').execute('select id, message_id, user_id, type from reports order by id desc limit 5').fetchall())"
```

| 해 보기 | 나와야 하는 것 | 결과 |
|---|---|---|
| 가려진 메시지 [욕설 아님] | `신고 접수됐어요`, DB에 `false_positive` 한 줄 | |
| 멀쩡한 메시지(다른 사람 것)에 마우스를 올려 신고 아이콘 | `이 메시지를 욕설로 신고할까요?` 확인 창 → `신고됨`, DB에 `missed` 한 줄 | |
| 확인 창에서 [취소] | 아무것도 저장되지 않음 | |
| 내가 쓴 멀쩡한 메시지 | 신고 아이콘이 없음 | |
| 가려진 메시지 | 신고 아이콘, 일정 만들기 아이콘이 없음 (가려진 말은 [욕설 아님]만) | |
| [욕설 아님]을 빠르게 여러 번 | DB에 한 줄만 | |
| 신고하고 새로고침한 뒤 또 신고 | 버튼은 다시 나오지만 DB에 새 줄이 안 생김 (서버가 막음) | |
| 서버를 끄고 [욕설 아님] | `서버에 연결할 수 없어요` 알림, `신고 접수됐어요`는 안 나옴 | |
| [보기] → [숨기기] | 원문이 보였다가 다시 가려짐 | |
| 이모지가 섞인 욕 (`👀 ㅅ1발`) | 이모지는 보이고 욕만 가려짐 | |
| 줄바꿈이 있는 긴 욕 문장 (폭 360) | 화면 밖으로 안 나감 | |

4. 민감도를 바꿔 봐요. **새 팀의 기본값은 "엄격하게"(3)예요** (server `cab400a`). 단계는 모델이 욕이라고 볼 점수 기준(0.9, 0.7, 0.5)만 바꿔요 (`backend/app/moderation/__init__.py`). 나쁜 뜻뿐인 말은 어느 단계에서나 가리고, 멀쩡한 뜻도 있는 말(꺼져, 새끼 등)은 모델이 그 문장을 보고 정해요. 모델이 연결되지 않으면 1, 2, 3이 똑같이 동작해요. 설정 화면의 설명("확실한 욕만 걸러요" 등)이 시연 때 사실인지 파트 1, 3과 확인하고, 다르면 `LEVELS`의 설명을 고쳐요.
5. 설정의 "가리기 + 방장 알림"과 화면의 `관리자 알림`은 말이 서로 달라요. 파트 3과 하나로 정하고 `MaskedMessage.tsx` 74줄이나 `settings/page.tsx` 24줄을 고쳐요. 실제로 알림을 보내는 기능은 없고, 방장 화면에 표시만 붙어요.

**확인할 것**: 위 표 세 개. 채운 표를 PR이나 팀 채팅에 올려요.

**끝 기준**: 세 표가 다 찼고, 다른 점은 고치거나 파트 1/3에게 알렸어요.

**넘길 것**: 욕 판정이 이상한 문장은 파트 3에게 알려요. **이 문장들을 C/D 테스트셋에 그대로 넣을지는 파트 3이 정해요.** 반대로 테스트셋 문장을 여기서 시험 삼아 쓰지 않아요.

**막히면**: 신고가 저장됐는지 모르겠으면 F12 → Network에서 `reports` 요청의 상태가 201인지 봐요. 404면 이미 지워진 메시지나 다른 팀의 메시지예요.

---

## 2-9. AI 성능 대시보드를 진짜 점수로 바꾸기

**왜**: 발표의 "개선 증명"(기획서 15장 4번)에 쓰는 화면이에요. 지금 이런 문제가 있어요. 가짜 CSV를 넣고 돌려서 확인했어요.

| 문제 | 파일 (`app/dashboard/page.tsx`) |
|---|---|
| CSV가 없으면 화면 모양을 보려고 넣어 둔 예시 점수가 보여요. 작은 글씨로 "예시 데이터"라고만 나와서 진짜 점수로 오해할 수 있어요 | `sample`, 108줄 |
| 욕설 F1 목표 90%, 날짜 정확도 목표 90%는 기획서 4.2에 **없는 목표**예요. 기획서의 욕설 목표는 "기존 필터보다 +20%p"예요 | 27, 40줄 |
| D셋(전부 멀쩡한 문장)의 F1이 `100% 달성`으로 나올 수 있어요. D셋에서 볼 것은 오탐률이에요 | 156줄 |
| 같은 이름으로 두 번 채점하면 막대가 두 개 나와요 | 158줄 |
| 유형별 점수(기획서 8.8)와 일정 응답 시간이 안 보여요. `schedule_by_type.csv`를 읽지 않아요 | 없음 |

**준비물**: 코드는 지금 할 수 있어요. 진짜 점수는 파트 3, 4가 `server/eval/reports/`에 CSV를 올린 뒤에 넣어요. 지금 server에는 이 폴더가 아직 없어요. 브랜치 `feat/dashboard-real`.

**순서**

1. 시험용 가짜 CSV를 만들어요. **커밋하지 않아요.** 점수는 아무렇게나 적은 숫자라 화면 확인에만 써요.

```sh
cd web && mkdir -p public/reports
printf 'label,testset,n,confirmed_f1,false_registration,cancel_recall,date_accuracy\nfake-a,test.jsonl,100,0.50,0.20,,0.80\nfake-b,test.jsonl,100,0.60,0.10,0.5,0.90\nfake-b,pilot.jsonl,20,0.55,0.0,,0.90\n' > public/reports/schedule.csv
printf 'label,testset,type,n,confirmed_f1,false_registration,cancel_recall,date_accuracy,latency_ms_mean,latency_ms_p95,input_tokens_mean,output_tokens_mean,cost_usd_per_1000\nfake-b,test.jsonl,ALL,100,0.60,0.10,0.5,0.9,5400,6100,,,\nfake-b,test.jsonl,유형A,20,0.5,0.1,,0.8,5400,6100,,,\nagreement,test.jsonl,ALL,20,0.5,0.1,,0.8,,,,,\n' > public/reports/schedule_by_type.csv
printf 'label,testset,n,f1,false_positive_rate,ms_per_msg\nfake-dict,moderation/test_C_human/test,120,0.40,,0.5\nfake-model,moderation/test_C_human/test,120,0.60,,40\nfake-dict,moderation/test_D_fp/test,100,0.0,0.09,0.5\nfake-model,moderation/test_D_fp/test,100,1.0,0.0,40\n' > public/reports/moderation.csv
```

2. **예시 점수 없애기.** `SOURCES`에서 ``sample: `…`,`` 두 덩어리를 지우고, `return { rows: parseCsv(SOURCES[k].sample), sample: true };`를 `return { rows: [], sample: true };`로 바꿔요. `예시 데이터 · 실제 CSV를 …` 글자는 `아직 점수 파일이 없어요`로 바꾸고, 그 `<span>`의 `title=…`도 지워요. 파일 맨 위 주석의 "예시 데이터" 설명도 맞게 고쳐요.
3. **기획서에 없는 목표 지우기.** `target: number`를 `target?: number`로 바꾸고, `날짜 정확도`와 `F1`의 `target: 0.9,`를 지워요. 그다음 `m.target`을 쓰는 곳을 "목표가 있을 때만"으로 바꿔요. 앞 단계에서 줄이 밀렸으니 왼쪽 글자를 검색해서 찾아요.

| 찾을 글자 | 바꿀 것 |
|---|---|
| `Math.max(m.target, …` | `Math.max(m.target ?? 0, …` |
| `const met = last && (…)` | `const met = last && m.target !== undefined && (…)` |
| `<span className="tabular-nums">목표 …</span>` | `{m.target !== undefined && <span …>…</span>}` |
| `{last && <span className={cn("inline-flex …` | `{last && m.target !== undefined && <span …` |
| `{/* 목표 점선 */}` 아래 `<line … />` | `{m.target !== undefined && <line … />}` |
| `…}}>목표</span>` | `{m.target !== undefined && <span …>목표</span>}` |

   욕설 F1의 목표(기획서 4.2)는 점선 대신 글로 적어요. `"높을수록 좋아요"}` 바로 뒤에 `{m.key === "f1" && " · C셋 목표는 기존 필터보다 +20%p"}`를 붙여요.
4. **D셋에서 F1 숨기기.** `const rows = cur?.rows.filter(…` 줄 아래에 이 두 줄을 넣고, 그 아래 화면 부분의 `src.metrics` 세 곳을 `metrics`로 바꿔요. 그래프와 "표로 보기"에서 같이 사라져요.

```ts
  // D셋은 전부 멀쩡한 문장이라 F1이 0 아니면 1만 나와요: F1은 빼고 오탐률을 봐요
  const metrics = src.metrics.filter((m) => !(m.key === "f1" && testset?.includes("test_D")));
```

5. **유형별 표 더하기.** 첫 조각은 주석에 적힌 자리 세 곳에 나눠 넣고, 둘째 조각(`ByType`)은 파일 맨 아래에 붙여요.

```tsx
  // 다른 useState 옆에:
  const [byType, setByType] = useState<Row[]>([]);
  // useEffect 안, load 정의 위에:
    fetch("/reports/schedule_by_type.csv", { cache: "no-store" })
      .then(async (res) => { if (res.ok) setByType(parseCsv(await res.text())); else await res.body?.cancel(); })
      .catch(() => {});
  // <details …> 바로 위에:
          {tab === "schedule" && <ByType rows={byType.filter((r) => r.testset === testset && r.label === rows.at(-1)?.label)} />}
```

```tsx
/** 가장 최근 버전의 유형별 점수 (schedule_by_type.csv). 파일이 없으면 아무것도 안 그려요 */
function ByType({ rows }: { rows: Row[] }) {
  if (rows.length === 0) return null;
  return (
    <section className="overflow-x-auto rounded-xl border bg-card p-4 text-sm">
      <h2 className="mb-2 text-base">유형별 점수 · {rows[0].label}</h2>
      <table className="w-full whitespace-nowrap text-right tabular-nums">
        <thead className="text-muted-foreground">
          <tr><th className="py-2 text-left font-normal">유형</th><th className="font-normal">n</th><th className="font-normal">확정 F1</th><th className="font-normal">오등록률</th><th className="font-normal">취소 감지율</th><th className="font-normal">평균 응답</th></tr>
        </thead>
        <tbody>
          {rows.map((r, i) => (
            <tr key={i} className="border-t">
              <td className="py-2 text-left">{r.type === "ALL" ? "전체" : r.type}</td>
              <td>{r.n}</td>
              <td>{fmt(num(r.confirmed_f1), "%")}</td>
              <td>{fmt(num(r.false_registration), "%")}</td>
              <td>{fmt(num(r.cancel_recall), "%")}</td>
              <td>{fmt(num(r.latency_ms_mean, "ms"), "ms")}</td>
            </tr>
          ))}
        </tbody>
      </table>
    </section>
  );
}
```

6. 아래 "확인할 것" 표를 다 채운 뒤에 가짜 CSV를 지우고 코드만 커밋해요.

```sh
cd web && rm -r public/reports && git status --short      # public/reports가 안 보여야 해요
npm run lint && npm run build
```

7. 파트 3, 4가 CSV를 올렸다고 알리면 진짜 파일을 복사해서 **따로 PR**로 올려요. 없는 파일은 `No such file`이라고 나오고 있는 것만 복사돼요.

```sh
cd server && git switch main && git pull && ls eval/reports
cd ../web && git switch main && git pull && git switch -c feat/dashboard-csv && mkdir -p public/reports
cp ../server/eval/reports/schedule.csv ../server/eval/reports/schedule_by_type.csv ../server/eval/reports/moderation.csv public/reports/
git add public/reports && git status --short
```

8. CSV는 손으로 고치지 않아요. 같은 이름이 두 번 있거나 숫자가 이상하면 파트 3(욕설) 또는 파트 4(일정)에게 다시 만들어 달라고 해요. CSV에 이 문서 1.5의 개발 중 숫자를 섞지 않아요.

**확인할 것**

| 해 보기 | 나와야 하는 것 | 결과 |
|---|---|---|
| CSV 없이 열기 | 점수가 하나도 안 보이고 `아직 점수 파일이 없어요`만 | |
| 가짜 CSV, 일정 AI | 오등록률 목표 5%, 날짜 정확도에 목표선 없음 | |
| 일정 AI에서 테스트셋 바꾸기 (`test.jsonl` ↔ `pilot.jsonl`) | 막대가 바뀌고, 유형별 표는 `test.jsonl`에서만 보임 (가짜 CSV에 `pilot.jsonl` 줄이 없어요). `agreement` 줄은 안 나옴 | |
| 욕설 AI, D셋 | 그래프와 "표로 보기"에 F1이 없고 오탐률(목표 3%)과 응답 시간만 | |
| 욕설 AI, C셋 | F1 칸 있음, 목표선 없음, `+20%p` 글이 보임 | |
| 빈 칸(`fake-a`의 취소 감지율) | "표로 보기"에 `-`, 그래프에는 막대 없음 (0%로 나오지 않음) | |
| CSV 맨 앞 줄만 있고 내용 없음 | 안내만, 멈추지 않음 | |
| 버전 이름이 아주 길거나 쉼표가 있음 (`"v2, 보강"`) | 이름이 두 줄까지, 쉼표에서 안 갈라짐 | |
| `schedule_by_type.csv`만 없음 | 표만 안 나오고 나머지는 정상 | |
| 폭 360 | 옆으로 밀리지 않음 (표만 안에서 밀려도 돼요) | |

**끝 기준**: 코드 PR이 머지됐고, 진짜 CSV를 넣은 화면에 예시 숫자가 하나도 없어요. 진짜 CSV가 아직 없으면 "CSV 대기"로 남겨요.

**넘길 것**: 파트 4에게 두 가지를 알려요. 일치도를 잴 때 `--report agreement.csv`만 쓰면 `schedule_by_type.csv`에는 `agreement` 줄이 그대로 쌓여요. `--by-type-report agreement_by_type.csv`도 같이 쓰면 안 섞여요. 그리고 4.2 목표와 대시보드 목표가 같은지 같이 확인해요. 욕설은 유형별 점수가 CSV에 없으니(화면에만 출력) 필요하면 파트 3에게 요청해요.

**막히면**: CSV를 넣었는데 안 바뀌면 주소창에 `http://localhost:3000/reports/schedule.csv`를 쳐서 파일이 보이는지 봐요. 안 보이면 `public/reports/` 위치가 틀린 거예요.

---

## 2-10. 실제 폰과 브라우저에서 끝까지 해 보기

**왜**: 지금까지 확인은 전부 PC의 화면 없는 크롬이었어요. 진짜 폰, 한글 키보드, 사파리에서는 아직 안 해 봤어요. 특히 아래는 코드로는 알 수 없어요.

* 한글을 치다가 Enter를 눌렀을 때 (특히 사파리)
* 폰 키보드가 올라왔을 때 입력창 위치 (화면 높이는 `h-dvh`, `components/app/AppFrame.tsx` 32줄)
* 메시지를 눌렀을 때 도구가 나오는지. 코드 주석에도 "포커스가 안 잡히는 폰이 있으면 state로 바꿔요"라고 적혀 있어요 (`MessageList.tsx` 218줄)
* 로그인 칸을 눌렀을 때 화면이 확대되는지. 입력칸 글자가 15px(`app/globals.css`의 `text-sm`)인데, 아이폰 사파리는 16px보다 작은 칸을 누르면 확대할 수 있어요. 채팅 입력창은 16px이에요
* 폰에서는 Enter가 전송이고 Shift+Enter가 없어서 줄바꿈을 못 해요. 괜찮은지 팀이 정해요

**준비물**

* 기기: 팀이 가진 아이폰(Safari), 안드로이드(Chrome), PC 크롬, PC 사파리(맥이 있으면). 같은 와이파이에 있어야 해요. 학교 와이파이는 기기끼리 막는 경우가 있어서, 그럴 때는 폰 핫스팟에 PC를 연결해요.
* 폰에서 내 PC에 접속하기. 배포 주소가 따로 정해지면 그 주소로 하고, 아래는 건너뛰어요.

```sh
# 1) 내 PC 주소 알아내기
ipconfig getifaddr en0          # 맥. 예: 192.168.0.12  (Windows는 ipconfig의 IPv4 주소)

# 2) server/.env 의 CORS_ORIGINS 를 이렇게 (내 주소로)
#    CORS_ORIGINS=["http://localhost:3000","http://192.168.0.12:3000"]

# 3) 서버를 다른 기기에서도 보이게 켜기 (make dev는 내 PC에서만 보여요)
#    같은 와이파이의 누구나 들어올 수 있으니 시험 데이터만 있는 DB로 하고, 끝나면 바로 꺼요
cd server && PYTHONPATH=ml/common uv run uvicorn app.main:app --app-dir backend --host 0.0.0.0

# 4) web/.env.local 을 이렇게 (내 주소로)
#    NEXT_PUBLIC_API_URL=http://192.168.0.12:8000
#    주소는 빌드할 때 들어가니까, 바꾸면 꼭 다시 build 해요

# 5) 다른 터미널에서, 실제 배포와 같은 모드로 켜기 (npm run dev는 먼저 꺼요)
cd web && npm run build && npm run start
```

  폰 브라우저에서 `http://192.168.0.12:3000`을 열어요. 끝나면 `.env`와 `.env.local`을 원래대로 돌리고 서버를 꺼요. 두 파일은 git에 올라가지 않지만, 화면 사진에 비밀번호나 `JWT_SECRET`이 찍히지 않게 조심해요.

**순서**

1. 기기마다 아래 시나리오를 1번부터 끝까지 해요. 되면 `O`, 안 되면 `X`와 번호를 적고 화면 사진을 남겨요.
2. `X`가 나오면 바로 고치지 말고, 끝까지 다 해 본 뒤 목록을 만들어요.
3. `app/`, `components/` 문제는 내가 작은 PR로 고치고, `lib/` 문제는 파트 1에게 보내요.
4. 고친 뒤 `X`였던 칸만 다시 해요.

**확인할 것 (기기 × 시나리오)**

| 번호 | 시나리오 | 나와야 하는 것 | 아이폰 Safari | 안드로이드 Chrome | PC 크롬 | PC 사파리 |
|---|---|---|---|---|---|---|
| 1 | 로그인 칸 누르기 | 글자가 잘 보이고, 확대돼도 로그인까지 막히지 않음 | | | | |
| 2 | 회원가입 → 온보딩 | 버튼이 키보드에 안 가려짐 | | | | |
| 3 | 초대 링크 열기 → 참가 | 새 팀 채팅으로 이동 | | | | |
| 4 | 채팅: `밥` 치고 바로 Enter | `밥`이 한 번만, 입력창이 빔 | | | | |
| 5 | 채팅: 받침 치는 중 Enter 연타 5번 | 마지막 글자가 따로 또 보내지지 않음 | | | | |
| 6 | 채팅: 5개 빠르게 연속 보내기 | 순서대로 5개, 빠진 것 없음 | | | | |
| 7 | 보내기 버튼 빠르게 두 번 | 한 개만 | | | | |
| 8 | 키보드 올라온 상태 | 입력창과 마지막 메시지가 보임 | | | | |
| 9 | 와이파이 끄고 보내기 | `전송하지 못했어요. 서버 연결을 확인해 주세요`, 글이 남음 | | | | |
| 10 | 와이파이 끈 사이 다른 사람이 여러 개 보냄 → 켜기 | 잠시 뒤 빠짐없이 채워짐, 중복 없음 | | | | |
| 11 | 화면을 잠갔다가 5분 뒤 켜기 | 다시 연결되고 놓친 메시지가 채워짐 | | | | |
| 12 | 다른 사람 메시지 한 번 누르기 | 오른쪽 위에 일정·신고 버튼이 나옴 | | | | |
| 13 | 그 상태에서 다른 곳 누르기 | 버튼이 사라짐, 안 보이는 버튼이 눌리지 않음 | | | | |
| 14 | 메시지로 일정 만들기 | 제목에 메시지 글이 들어간 팝업, 저장 후 캘린더에 보임 | | | | |
| 15 | AI 카드(모델이 없으면 2-1의 시험 카드) [캘린더에 추가], [수정], [무시] | 버튼이 안 잘리고 처리됨 | | | | |
| 16 | 캘린더 날짜 누르기, 일정 끌어 옮기기 | 팝업이 열리고, 옮긴 날짜가 새로고침 뒤에도 유지 | | | | |
| 17 | 할 일 추가, 체크, 담당자 바꾸기, 삭제 | 새로고침 뒤에도 유지 | | | | |
| 18 | ☰ 메뉴 열고 닫기, 화면 이동 | 화면을 옮기면 서랍이 닫힘 | | | | |
| 19 | 설정 → 초대 링크 [복사] | 복사되거나 링크 창이 뜸 (2-7) | | | | |
| 20 | 마이페이지: 어둡게, 글자 크게 | 새로고침 뒤에도 유지, 글자 안 잘림 | | | | |
| 21 | 욕설 메시지 [보기], [욕설 아님] | 2-8과 같음 | | | | |
| 22 | 로그아웃 → 다른 계정 로그인 | 앞 사람 글이 안 보임 (2-5) | | | | |
| 23 | 빈 팀 (채널 1개, 메시지 0개) | `첫 메시지를 보내 보세요`, 할 일은 `할 일이 없어요` | | | | |
| 24 | 이름 50자 팀원, 채널 이름 50자 | 글자가 …으로 줄고 화면이 안 밀림 | | | | |
| 25 | 없는 주소 `/w/99999`, `/w/1/c/99999` | `이 프로젝트를 찾을 수 없어요`, `없는 채널이거나 들어갈 수 없는 채널이에요` | | | | |

실패 목록 양식:

| 번호 | 기기와 버전 | 무엇을 했나 | 나온 것 | 나와야 하는 것 | 고칠 사람 |
|---|---|---|---|---|---|
| | | | | | |

**끝 기준**: 네 기기(없는 기기는 "기기 없음")의 칸이 다 찼고, `X`마다 고칠 사람이 정해졌어요. 시연에 쓸 기기에는 `X`가 없어요.

**넘길 것**: 표와 실패 목록을 모두에게. `lib/` 문제는 파트 1에게 따로.

**막히면**

* 폰에서 화면은 뜨는데 로그인이 `서버에 연결할 수 없어요`면: `CORS_ORIGINS`에 폰이 연 주소가 그대로 있는지, `NEXT_PUBLIC_API_URL`을 바꾼 뒤 다시 build 했는지, 서버를 `--host 0.0.0.0`으로 켰는지 순서대로 봐요.
* 화면 자체가 안 뜨면: PC 방화벽 허용 창이 떴는지, 폰이 같은 와이파이인지, 핫스팟으로도 되는지 봐요.
* 5번에서 마지막 글자가 한 번 더 보내지면: `MessageInput.tsx` 103줄의 `isComposing` 검사가 그 브라우저에서 안 통하는 거예요. 기기 이름과 순서를 적어서 파트 1과 같이 봐요.

---

## 2-11. 서버 API가 바뀌면 타입 다시 만들기

**왜**: 파트 1이 모델을 연결하면서 API를 또 바꿀 수 있어요. 타입이 옛날 것이면 빨간 줄 없이 틀린 값을 보내게 돼요. 지금은 최신이라 할 일이 없어요. **파트 1이 "API가 바뀌었다"고 알릴 때만** 해요.

**준비물**: server와 web이 같은 폴더에 나란히. 파트 1의 알림.

**순서**

```sh
cd web && git switch main && git pull && git switch -c chore/api-types
(cd ../server && git switch main && git pull && uv sync && make openapi) && npm run gen:api
git diff --stat                       # lib/api/schema.d.ts 만 바뀌어야 해요
npm run lint && npm run build && npm test
git add lib/api/schema.d.ts           # 커밋할 때. 이 파일은 AGENTS.md가 정한 명령으로 만든 거라 올려도 돼요
```

1. `git diff lib/api/schema.d.ts`를 읽고 무엇이 생기고 없어졌는지 한 줄씩 적어요.
2. `npm run build`에서 빨간 오류가 나면 그 화면을 고쳐요. `lib/api/`, `lib/ws/`에서 오류가 나면 파트 1에게 알려요.
3. `lib/api/schema.d.ts`는 **손으로 고치지 않아요.**

**확인할 것**

| 해 보기 | 나와야 하는 것 |
|---|---|
| 바뀐 API를 쓰는 화면 열기 | 오류 없이 동작 |
| `make openapi`가 실패 | `uv sync`를 다시 하고, 그래도 안 되면 오류 글자를 파트 1에게 |

**끝 기준**: `schema.d.ts`만 바뀐 PR이 머지되고 build가 통과해요. `lib/api/`는 파트 1 담당이라 PR 리뷰를 꼭 받아요.

**넘길 것**: PR을 파트 1에게.

**막히면**: `openapi.json`을 찾을 수 없다는 오류면 server 레포가 `web`과 같은 폴더(`../server`)에 있는지 봐요.

---

## 2-12. 시연 리허설

**왜**: 기획서 17.6의 마지막 주차 일이에요. 시연은 5분이고(기획서 15장), 화면에서 막히면 되돌릴 시간이 없어요.

**준비물**: 2-6, 2-7, 2-9, 2-10 끝. 파트 1이 모델을 연결했다는 알림. 2-1의 새 팀(시연 팀 `make seed`를 쓰면 함정 1, 2를 먼저 해결). 창 폭 1280px 이상인 노트북 (그래야 오른쪽 일정 칸이 보여요).

**순서**

1. 기획서 15장 순서대로 한 번 끝까지 해요. 누가 어떤 계정으로 어떤 말을 보낼지 파트 4와 대본을 정해요. **대본 문장은 테스트셋에서 가져오지 않아요.**
2. 매번 새 팀을 만들어서 해요. 만들 때 AI 동의를 체크하고, 계정 4개(`minjun`, `seoyeon`, `jiho`, `haeun`)를 초대 링크로 모두 넣어요. 같은 팀에서 다시 하면 이미 처리한 카드와 같은 내용은 다시 안 떠요 (`pipeline.py`가 같은 카드를 막아요).
3. 카드가 뜨는 데 걸리는 시간을 재요. 서버는 마지막 메시지 10초 뒤, 또는 5번째 메시지마다 분석하고, 그다음 모델 응답 시간이 더 걸려요.
4. 아래 표를 리허설마다 채워요.

| 장면 (기획서 15장) | 누가, 무엇을 | 나와야 하는 것 | 걸린 시간 | 1회 | 2회 |
|---|---|---|---|---|---|
| 데모 1: 협상 | 4명이 대화 | 확정 전에는 카드 없음 | | | |
| 데모 1: 확정 | "…로 확정" | `새 일정` 카드 | | | |
| 데모 1: 등록 | [캘린더에 추가] | `등록됨`, 오른쪽 칸과 캘린더에 노란 일정 | | | |
| 데모 1: 번복 | 시각 바꾸기 | `일정 변경` 카드, 바뀌기 전 일정이 줄로 보임 | | | |
| 데모 1: 취소 | 취소하기 | `일정 취소` 카드 → `취소됨` | | | |
| 데모 2: 가림 | `ㅅ1발`, `tlqkf` | 검은 줄과 [보기] | | | |
| 데모 2: 통과 | `시발점` | 그대로 보임 | | | |
| 개선 증명 | 대시보드 | 진짜 CSV 점수만 보임 | | | |

5. 일부러 망가뜨려 봐요.

| 사고 | 해야 할 일 | 실제로 해 본 결과 |
|---|---|---|
| 와이파이가 끊김 | 켜면 다시 채워지는지 기다림 | |
| 카드가 1분 넘게 안 뜸 | 대본의 다음 문장으로 넘어감. 파트 1에게 로그 확인 | |
| 서버가 죽음 | 파트 1이 다시 켜는 동안 `/w/demo` 화면으로 설명 (데모라고 말하기) | |
| 팀원이 캘린더에 직접 추가한 일정이 안 보임 | 새로고침 (직접 추가한 일정은 실시간으로 안 와요) | |
| 로그인이 풀림 | 다시 로그인. 리허설 전에 모든 계정을 새로 로그인해 둠 (로그인은 7일 뒤 풀려요) | |

**확인할 것**: 위 두 표. 그리고 시연 화면에 "준비 중", "파트 1", 영어 오류, 예시 점수가 하나도 안 나오는지 봐요.

**끝 기준**: 처음부터 끝까지 막힘 없이 두 번 연속 성공했고, 표가 다 찼어요.

**넘길 것**: 표를 모두에게. 카드 응답 시간은 파트 1에게 (기획서 4.2 목표는 3초, 1.5의 측정은 서버에서 평균 2초 안팎). 발표 자료에 쓸 화면 사진은 파트 4에게.

**막히면**: 카드가 안 뜨면 (1) AI가 켜져 있는지, (2) 켠 **뒤에** 보낸 메시지인지, (3) 시연 팀이면 함정 1을 했는지, (4) 날짜나 시간 말이 들어간 문장인지 순서대로 봐요. 다 맞으면 파트 1에게 서버 터미널 화면을 보내요.
