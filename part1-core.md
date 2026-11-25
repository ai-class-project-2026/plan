# 파트 1. 아키텍처 + AI 모델 학습 + 통합 담당

## 한 줄 요약

뼈대를 세우고, 두 AI 모델(일정, 욕설)을 직접 학습시키고, 모든 파트의 결과물을 하나의 데모로 묶는 파트.

## 맡는 것

| 영역 | 내용 | 레포 |
|---|---|---|
| 뼈대 | 두 레포 구조, `.env` 관리, API 스키마(`schemas.py`) 확정 | web, server |
| 백엔드 | DB(SQLAlchemy + Alembic), 인증(이메일 + JWT), REST API, WebSocket 채팅 | server |
| 웹 연결부 | API 클라이언트, 실시간 훅(`useMessages` 등). 화면은 파트 2 | web |
| 일정 AI | 합성 데이터 생성, LoRA 파인튜닝, GGUF 4bit 서빙, 상태 추적(`tracker.py`), 기존 일정 대조(`reconcile.py`) | server |
| 욕설 AI 학습 | KcELECTRA 파인튜닝(1차/2차), ONNX 변환, `/moderate`에 연결. 학습 데이터는 파트 3 | server |
| 통합 | 메시지 저장 → 욕설 판정 / 일정 추출 → WebSocket 카드까지 전체 흐름 | web, server |
| 지원 | 모든 PR 리뷰, 파트별 템플릿 코드, 주 1회 30분 페어 작업 | |

## 지금 상태 (2026-11-16)

**된 것**

* server: DB 스키마와 마이그레이션, 인증, 워크스페이스/채널/메시지/일정/할 일/신고 API, WebSocket, 시연용 seed
* server: 일정 파이프라인 `trigger → dateparse_ko 주석 → tracker(LLM) → reconcile`, 10초 디바운스(대화가 멈췄을 때만 실행)
* server: 욕설 판정 뼈대(정규화 → 사전 필터 → ONNX 분류기 자리), `/moderate`, `/schedule`
* server: 학습/평가 스크립트 `ml/schedule/synth/generate.py`, `ml/schedule/train_lora.py`, `ml/moderation/train.py`, `ml/moderation/export_onnx.py`, `eval/*/score.py`
* web: 타입 생성된 API 클라이언트, 인증 헬퍼, 실시간 채팅 / AI 카드 훅
* 학습 전 모델(qwen2.5:3b, Ollama)로 채팅 → 카드 흐름 동작 확인 (응답 3.2초)

**남은 것**

* 일정 AI: 합성 데이터 3,000개 생성 → LoRA 1차/2차 학습 → GGUF 변환 후 서버 연결
* 일정 AI: 약점 유형(번복, 취소, 부분 참석) 보강 데이터, `tracker` / `reconcile` 개선
* 욕설 AI: 파트 3의 `train.jsonl`로 1차/2차 학습 → ONNX 변환 → `MODERATION_ONNX_PATH` 연결
* 통합: 웹 화면(파트 2)과 실제 API 연결, 버그 수정, 최종 평가, 라이브 데모

## 주차별

| 주차 | 할 일 | 마일스톤 |
|---|---|---|
| 1 | 레포 뼈대, DB 스키마, API 스키마 | 완료 |
| 2 | 인증, WebSocket 채팅 + 실시간 훅, AI API 뼈대 | 완료 |
| 3 | 합성 데이터 생성기 실행, 욕설 1차 학습 | |
| 4 | 일정 LoRA 1차 학습 | 중간 점검 |
| 5 | 상태 추적 개선, `/schedule` 실제 모델 연결, 욕설 2차 학습 | |
| 6 | 전체 통합, 욕설 ONNX 변환, `/moderate` 연결 | 전체 기능 연결 |
| 7 | 버그 수정, 최종 평가 | 코드 프리즈 |
| 8 | 라이브 데모 | 발표 |

4주차에 채팅이 안 되면 파트 4의 디스코드 봇을 앞당겨 AI 시연 경로를 먼저 확보한다.

## 다른 파트와 주고받는 것

| 상대 | 받는 것 | 주는 것 |
|---|---|---|
| 파트 2 (웹) | 화면 | API 클라이언트, 실시간 훅, 바뀐 API 타입(`make openapi`) |
| 파트 3 (욕설) | `train.jsonl`, 단어 목록, 채점 결과와 약점 | 학습된 모델, ONNX 서빙 |
| 파트 4 (날짜/데이터) | `dateparse_ko`, 실제 테스트셋, 점수 CSV | `ANTHROPIC_API_KEY`(기준선 채점용), 서버에 연결된 모델, 라벨 교차 검수 |

## 완료 기준

* 학습된 일정 모델이 `/schedule`에 붙어 있고, 실제 테스트셋에서 기획서 4.2 목표 달성 여부가 `eval/reports/schedule.csv`에 남아 있음
* 학습된 욕설 모델(ONNX)이 `/moderate`에 붙어 있고, A\~D 점수가 `eval/reports/moderation.csv`에 남아 있음
* 웹에서 채팅 → 욕설 가림 → AI 일정 카드 → 캘린더 등록이 끊김 없이 시연됨

---

# 명령 모음

```sh
cd server
uv sync --group ml
make migrate && make seed && make dev        # http://localhost:8000/docs
```

## 일정 AI

```sh
# 1) 합성 데이터 (ANTHROPIC_API_KEY 필요, 먼저 --n 20으로 비용 확인)
uv run --group ml python ml/schedule/synth/generate.py --n 3000 --out data/schedule/synth/train.jsonl

# 2) LoRA 학습 (Colab T4/L4): ml/schedule/train_lora.py 맨 위 주석
#    → 어댑터 병합 → GGUF q4_K_M → llama-server -m model.gguf --port 8080

# 3) 서버에 연결
SCHEDULE_LLM_URL=http://localhost:8080 SCHEDULE_MODEL_VERSION=lora-v1 make dev

# 4) 평가
uv run --group ml python eval/schedule/run.py <testset> preds.jsonl --target server
uv run python eval/schedule/score.py <testset> preds.jsonl --label lora-v1
```

## 욕설 AI

```sh
# 1) 학습 (Colab): 파트 3이 준 data/moderation/train.jsonl
python ml/moderation/train.py --train data/moderation/train.jsonl --out kcelectra-v1

# 2) ONNX 변환 → 서버에 연결
python ml/moderation/export_onnx.py kcelectra-v1 onnx-v1
uv sync --group onnx
MODERATION_ONNX_PATH=onnx-v1 make dev

# 3) 평가 (파트 3과 같이 확인)
uv run python eval/moderation/score.py data/moderation/test_C_human/sample.jsonl --label kcelectra-v1
```

## 작업 규칙

* API 형식을 바꾸면 `make openapi`로 `openapi.json`을 갱신하고 파트 2에 알린다 (기획서 16.5)
* 모델 가중치, 합성 데이터, 공개 데이터 원본은 커밋하지 않는다. 가중치는 HF Hub에 버전별로 올린다 (기획서 16.6)
* 실제 테스트셋은 학습과 합성 데이터 프롬프트에 절대 넣지 않는다
* AI로 한 작업은 `ai-sessions/part1/`에 남는다 (기획서 17.8)
