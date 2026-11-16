# 파트 4. 날짜 규칙 + 테스트 데이터 + 발표 담당

## 한 줄 요약

AI가 날짜를 정확히 계산하게 만들고, AI가 잘하는지 시험지를 만들고 채점하고, 마지막에 발표까지 책임지는 파트예요.

## 뭘 만드나요?

* **날짜 계산기**: "다음주 화", "모레", "이번 주말"을 실제 날짜로 바꾸는 코드
  * 예시: 오늘이 9/30(수)이면 "다음주 화" → 10/6
  * 규칙은 기획서 8.5에 이미 다 정해져 있어서, 그대로 코드로 옮기면 돼요.
* **시험지(테스트셋)**: 친구들이랑 단톡 역할극을 해서 실제 대화 100개 이상을 만들고, "이 대화에서 확정된 일정은 뭔지" 정답을 달아요.
* **채점과 그래프**: AI 버전별 점수를 정리해서 "이만큼 좋아졌다" 그래프를 만들어요.
* **디스코드 봇**: 웹이 늦어질 때를 대비한 비상용 시연 도구.
* **발표 자료**

## 왜 중요한가요?

* 날짜를 틀리면 일정 AI 전체가 틀려요. 작지만 핵심 부품이에요.
* 시험지가 없으면 "개선했다"는 말을 증명할 수 없어요. 이 파트가 과제의 증거를 만들어요.

## 어렵지 않나요?

* 날짜 계산기는 정답이 명확해서 초보자에게 딱 좋아요. "입력: 9/30, 다음주 화 → 정답: 10/6" 같은 예시를 먼저 정하고, 그대로 나올 때까지 코드를 고치면 돼요.
* 역할극과 라벨링은 코딩보다 꼼꼼함이 중요해요.

## 진행 순서

| 주차 | 할 일 |
|-----|-----|
| 1 | 파일럿 20개를 상용 LLM에 넣고 채점 → 기존 AI가 뭘 틀리는지 확인 |
| 2 | 날짜 기본 규칙 (오늘, 내일, 모레, 요일) |
| 3 | 나머지 규칙 (다음주, 주말, "그 전주 목요일"), 예시 100개 |
| 4 | 시나리오 카드 만들기 → 역할극 → 정답 달기 |
| 5 | 평가 돌리고 점수 CSV 정리 |
| 6 | 디스코드 봇 |
| 7 | 개선 전후 그래프 |
| 8 | 발표 자료, 발표 |

## 첫 주에 할 일

- [ ] 기획서 8.3(정답 기준)과 8.5(날짜 규칙) 꼼꼼히 읽기
- [ ] 파이썬 datetime으로 "오늘 + 3일", "이번 주 금요일" 계산해보기
- [ ] 파일럿 대화 20개를 `data/schedule/pilot/pilot.jsonl` 형식으로 옮기고 `eval/schedule/run.py` → `score.py`로 채점, 결과 팀에 공유 (아래 "실제로 시작하기" 3번)

## 알아두면 좋은 용어

* **정규화**: 제각각인 표현을 하나의 형식으로 통일하는 것. "다음주 화", "담주 화욜" → 2026-10-06
* **기준일**: "내일"이 언제인지 계산하려면 "오늘"이 필요해요. 그 "오늘"이 기준일이에요.
* **라벨링**: 대화마다 정답(확정된 일정)을 달아주는 작업. 두 명이 따로 달고, 다르게 단 것만 토론해서 정해요.

## 이렇게 되면 끝이에요

* 날짜 예시 100개 전부 정답
* 실제 테스트셋 100개 이상 완성
* 발표 자료 완성

## 이 파트를 하면 얻는 것

정답을 먼저 정하고 코드 짜는 습관, AI를 어떻게 평가하는지에 대한 감각, 그리고 발표 경험.

---

# 실제로 시작하기

## 1. 내 컴퓨터에서 띄우기

```sh
git clone https://github.com/ai-class-project-2026/server.git
cd server
uv sync --group ml       # uv: https://docs.astral.sh/uv/
```

## 2. 날짜 계산기 (`ml/common/dateparse_ko/`)

함수 하나만 완성하면 돼요. 모양은 이미 정해져 있어요.

```python
from dateparse_ko import parse
parse("내일 7시 ㄱㄱ", date(2026, 9, 30))
# → [Mention(start=0, end=2, date=2026-10-01), Mention(start=3, end=5, time=19:00)]
```

* `start`, `end`: 문장 안에서 그 표현의 위치 (`"내일 7시 ㄱㄱ"[0:2] == "내일"`)
* `date` / `time` / `time_text` 중 해당하는 것만 채워요. "점심시간", "방과후"는 `time_text`에 그대로
* 8~11시처럼 오전/오후가 애매하면 `time=None`, `time_text="8시"`로 두기 (기획서 8.5)
* 오늘/내일/모레/글피는 이미 되어 있어요. 나머지가 할 일이에요

이 함수는 학습 데이터 검증(`ml/schedule/synth/generate.py`)과 실제 서비스(`backend/app/schedule/prompt.py`, AI에게 "[날짜: 내일=2026-10-01(목)]" 주석을 붙여줌) **양쪽에서 같이** 써요. 그래서 이 파일이 틀리면 일정 AI 전체가 틀려요.

### 순서 (정답 먼저)

1. `ml/common/dateparse_ko/examples.py`의 `EXAMPLES`에 기획서 8.5 표의 규칙마다 "입력 → 정답 날짜" 예시를 여러 개씩, 총 100개 이상 추가해요. 기준일은 2026-09-30(수)
2. `uv run python ml/common/dateparse_ko/examples.py`를 돌리면 틀린 줄에 ✗가 떠요. `parse()`를 고쳐서 ✗를 없애요. 하나만 따로 보고 싶으면: `uv run python -c "import sys, datetime as dt; sys.path.insert(0, 'ml/common'); from dateparse_ko import parse; print(parse('다음주 화', dt.date(2026, 9, 30)))"`
3. 예시가 전부 정답이 나오면 끝이에요

지금 상태 (기준일 9/30 수):

| 입력 | 정답 | 지금 |
|---|---|---|
| 오늘 보자 / 내일 ㄱㄱ / 모레 어때 / 글피는 안돼 | 9/30 / 10/1 / 10/2 / 10/3 | 됨 |
| 다음주 화 | 10/6 | 안 됨 |
| 금요일 ㄱ | 10/2 | 안 됨 |
| 이번 주말 | 10/3 | 안 됨 |
| 10/15 회의 | 10/15 | 안 됨 |

## 3. 시험지와 채점

### 데이터 한 줄 형식 (파일럿, 실제 테스트셋 공통)

```json
{"type": "revise",
 "ref_datetime": "2026-10-12T21:10:00+09:00",
 "participants": ["민준", "서연", "지호", "하은"],
 "dialogue": [{"speaker": "민준", "text": "화요일 어때?", "ts": "2026-10-12T21:05:00+09:00"}, "..."],
 "existing_events": [],
 "events": [{"title": "팀 회의", "date": "2026-10-15", "weekday": "목", "start": "19:00",
             "time_text": null, "location": "디스코드", "status": "confirmed",
             "attendees": ["민준", "서연", "지호", "하은"], "absent": [], "note": null}]}
```

* `events`가 정답이에요 (기획서 8.2 형식, 8.3 가이드라인대로)
* `type`은 유형별 점수용: `confirm`, `proposal_only`, `revise`, `cancel`, `partial_absent`, `relative_date`, `not_schedule`
* `existing_events`: 취소/번복 대화라면 대화 전에 캘린더에 이미 있던 일정
* 파일 위치: 파일럿 `data/schedule/pilot/pilot.jsonl`, 실제 테스트셋 `data/schedule/real_test/test.jsonl` (**절대 학습에 쓰지 않기**)

### 채점

```sh
# 1) 상용 LLM 기준선 (ANTHROPIC_API_KEY 필요, 파트 1에게 받기)
uv run --group ml python eval/schedule/run.py data/schedule/pilot/pilot.jsonl preds_claude.jsonl --target claude
# 2) 우리 모델 (서버에 모델이 연결된 뒤, make dev 켠 상태에서)
uv run --group ml python eval/schedule/run.py data/schedule/pilot/pilot.jsonl preds_ours.jsonl --target server
# 3) 점수
uv run python eval/schedule/score.py data/schedule/pilot/pilot.jsonl preds_claude.jsonl --label claude-zeroshot
```

나오는 점수: `confirmed_f1`(확정 일정 F1), `false_registration`(오등록률, 가장 중요), `cancel_recall`(취소 감지율), `date_accuracy`, `f1_by_type`. 매번 `eval/reports/schedule.csv`에 한 줄씩 쌓이고, 이게 대시보드와 발표 그래프의 원본이에요.

### 라벨링 교차 검수 팁

두 명이 같은 대화에 정답을 따로 달아 `a.jsonl`, `b.jsonl`로 저장한 다음 `score.py a.jsonl b.jsonl --label agreement --report /tmp/x.csv`를 돌리면 둘이 얼마나 일치하는지 숫자로 나와요. 다른 줄만 모아서 토론하면 돼요.

## 4. 디스코드 봇 (플랜 B, 6주차)

* 위치: `bots/discord/bot.py` (뼈대 있음, `TODO(part 4)` 두 곳). 실행: `uv run --group bot --env-file .env python bots/discord/bot.py`
* 채널에 `!일정`을 치면 최근 30개 메시지로 `/schedule`을 부르고, 다른 메시지는 `/moderate`로 검사해요
* 서버의 AI API만 부르면 돼요: 메시지가 오면 `POST /moderate`, 최근 대화를 모아 `POST /schedule` (요청/응답 모양은 http://localhost:8000/docs 참고)
* 헤더에 `X-API-Key: <서버 .env의 AI_API_KEY>`를 붙여요
* 봇 토큰은 코드에 넣지 말고 `.env`에 (`DISCORD_TOKEN=`)

## 5. 작업 규칙

* 브랜치를 따서 작게 PR. PR 전에 `make dev`로 서버가 뜨는지 확인
* 테스트셋 파일은 커밋해요 (작고 다시 만들 수 없어서). 합성 데이터는 커밋하지 않아요
* 하루 이상 막히면 바로 물어보기
