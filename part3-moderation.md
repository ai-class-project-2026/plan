# 파트 3. 욕설 AI 담당

## 한 줄 요약

"ㅅ1발", "씨@발", "tlqkf" 같은 돌려 쓴 욕설까지 잡는 AI를 만드는 파트예요.

## 뭘 만드나요?

* **욕설 변형기**: 멀쩡한 욕설 데이터를 코드로 비틀어서 우회 욕설 데이터를 대량으로 만들어요.
  * "시발" → "시1발", "씨@발", "ㅅㅂ", "시 발", "tlqkf" ...
* **AI 학습**: 이 데이터로 이미 있는 한국어 AI 모델(KcELECTRA)을 추가로 가르쳐요.
* **성적표**: 기존 욕설 필터랑 우리 모델 중 누가 더 잘 잡는지 점수로 비교해요.

## 왜 중요한가요?

이 과제의 핵심은 "기존 AI의 약점을 내가 고쳤다"는 걸 숫자로 보여주는 것이에요. 이 파트가 그 증거 중 하나를 통째로 만들어요.

## 어렵지 않나요?

생각보다 괜찮아요.

* 변형기는 그냥 파이썬 문자열 다루기예요. "글자 사이에 숫자 끼워 넣기" 같은 규칙을 함수로 만드는 거예요.
* 학습 코드(`ml/moderation/train.py`)는 이미 있어요. Colab에서 데이터 경로만 바꿔 실행하면 돼요.
* 서버에 붙이는 작업(ONNX 변환)은 전문가랑 같이 해요.

## 진행 순서

| 주차 | 할 일 |
|-----|-----|
| 1 | 공개 욕설 데이터 모으기, 친구들한테 우회 욕설 30개 받아서 기존 필터에 넣어보기 |
| 2 | 변형 규칙 5종 만들기 (숫자 치환, 특수문자, 초성, 띄어쓰기, 늘이기) |
| 3 | 나머지 5종 (자모 분리, 받침 분리, 유사 발음, 영문 자판, 복합), 1차 학습 |
| 4 | "필터 뚫기 챌린지"로 테스트 문장 모으기, 오탐 유도 문장 100개 만들기 |
| 5 | 2차 학습, 화이트리스트("시발점", "18세" 같은 멀쩡한 단어) 정리 |
| 6 | 전문가와 함께 서버에 붙이기 |
| 7\~8 | 최종 점수표, 시연 준비 |

## 첫 주에 할 일

- [ ] 파이썬 문자열/정규식 기초 복습
- [ ] 한글 자모 분해 라이브러리(jamo 또는 hgtk)로 "시발" → "ㅅㅣㅂㅏㄹ" 해보기
- [ ] 기획서 9.4에 있는 공개 데이터 다운로드하고 몇 줄 열어보기
- [ ] 친구들한테 "이 필터 뚫어봐" 하고 우회 욕설 30개 받기

## 알아두면 좋은 용어

* **데이터 증강**: 있는 데이터를 변형해서 학습 데이터를 늘리는 것. 라벨(욕설 여부)이 그대로 따라와서 사람이 일일이 표시할 필요가 없어요.
* **파인튜닝**: 이미 한국어를 아는 AI에게 "욕설 찾기"만 추가로 가르치는 것.
* **오탐 / 미탐**: 오탐은 멀쩡한 말을 욕으로 잡은 것("시발점"), 미탐은 욕을 놓친 것.
* **F1 점수**: 오탐과 미탐을 둘 다 고려한 종합 점수. 1에 가까울수록 좋아요.

## 이렇게 되면 끝이에요

* 테스트셋 4개(A\~D)에 대한 점수표가 나옴
* 서버에 문장을 넣으면 "욕설 맞음/아님"이 나옴

## 이 파트를 하면 얻는 것

데이터 만들기 → 학습 → 평가까지 AI 개발 한 사이클을 처음부터 끝까지 직접 해본 경험.

---

# 실제로 시작하기

## 1. 내 컴퓨터에서 띄우기

```sh
git clone https://github.com/ai-class-project-2026/server.git
cd server
uv sync --group ml       # uv: https://docs.astral.sh/uv/
uv run pytest            # 전부 통과하면 준비 끝
```

## 2. 지금 상태 (여기서 출발해요)

욕설 판정은 `backend/app/moderation/`에 있고, 채팅에서 메시지를 보낼 때마다 `moderate()`가 불려요. 지금은 아주 기본적인 버전이에요.

```sh
uv run python eval/moderation/score.py data/moderation/test_C_human/sample.jsonl --label dict-v0
uv run python eval/moderation/score.py data/moderation/test_D_fp/sample.jsonl --label dict-v0
```

| 들어오는 문장 | 지금 결과 | 원인 |
|---|---|---|
| 씨@발, 시 발, ＴＬＱＫＦ | 잡음 | 기호·공백 제거, 전각 문자 정리는 이미 됨 |
| ㅅ1발, 시이이발, ㅅㅣ발 | **못 잡음** | 숫자 치환, 늘이기, 자모 재조합이 없음 → 이 파트가 할 일 |
| 수박씨 발라 먹어 | **잘못 잡음** (오탐) | 공백을 지우니 "씨발"이 생김 → 화이트리스트에 넣을 일 |

이 표를 개선 전 기록으로 남겨두면 발표 자료로 쓸 수 있어요.

## 3. 파일별 할 일

| 파일 | 할 일 | 지켜야 할 약속 |
|---|---|---|
| `backend/app/moderation/normalize.py` | 숫자/기호 → 한글 후보, 자모 재조합(ㅅㅣ발→시발), 영문 자판(tlqkf→시발), 반복 축약(시이이발→시발) | `(정규화된 글자, index_map)` 반환. `index_map[i]`는 정규화 글자 i가 **원문 몇 번째 글자**에서 왔는지. 이게 맞아야 화면에서 정확한 위치를 가려요 |
| `backend/app/moderation/badwords.txt` | 욕설 단어 목록 (정규화된 형태로, 한 줄에 하나) | |
| `backend/app/moderation/whitelist.txt` | 멀쩡한 단어 (시발점, 수박씨발라…) | 정규화된 형태로 |
| `ml/moderation/augment/__init__.py` | 우회 변형 규칙 10종 (기획서 9.3). `insert_digit` 예시가 하나 있어요 | 함수 모양: `(text, rng) -> text` |
| `backend/tests/test_augment.py` | 규칙마다 "입력 → 출력" 테스트 | `uv run pytest`로 확인 |
| `data/moderation/test_C_human/`, `test_D_fp/` | 필터 뚫기 챌린지(C), 오탐 유도 문장 100개(D) | 한 줄에 `{"text": "...", "label": 0 또는 1, "type": "숫자 치환"}` |
| `data/README.md` | 공개 데이터 출처와 라이선스 기록 | UnSmile은 평가 전용 (변경 금지 라이선스) |

정규화 함수를 고쳤으면 바로 확인해 보세요.

```sh
uv run python -c "import sys; sys.path.insert(0,'backend'); from app.moderation import moderate; print(moderate('ㅅ1발 뭐하냐'))"
```

서버를 띄웠으면(`make dev`) http://localhost:8000/docs 의 `POST /moderate`에서도 직접 넣어볼 수 있어요.

## 4. 모델 학습 (3주차~)

1. 공개 데이터 + 증강으로 `data/moderation/train.jsonl` 만들기 (`{"text", "label"}`). 원본은 커밋하지 않아요 (`data/moderation/raw/`는 gitignore)
2. Colab에서 `ml/moderation/train.py` 실행 (맨 위 주석에 명령어 있음). 모델은 KcELECTRA, 입력은 "원문 + 정규화문" 문장 쌍
3. `ml/moderation/export_onnx.py`로 ONNX 변환 → **파트 1과 같이** 서버에 붙여요 (`MODERATION_ONNX_PATH`)
4. 모델이 붙으면 같은 `eval/moderation/score.py`가 사전 필터 + 모델 전체를 채점해요. `--label`에 버전 이름(`kcelectra-v1`)을 넣으면 `eval/reports/moderation.csv`에 쌓여서 대시보드 그래프가 돼요

목표 (기획서 4.2): C셋 F1이 기존 필터보다 +20%p, D셋 오탐률 3% 이하, 메시지당 100ms 이내 (`ms_per_msg`로 확인).

## 5. 작업 규칙

* 브랜치를 따서 작게 PR (`feat/augment-chosung`처럼). PR 전에 `uv run pytest`와 `uv run ruff check .` 통과 확인
* 단어 목록이나 규칙을 바꿨으면 PR 설명에 score.py 결과(전/후)를 붙여요
* 하루 이상 막히면 바로 물어보기
