# 파트 3 (욕설) 할 일

[전체 정리](handoff-2026-11-25.md)에서 이어지는 문서예요. 1.1\~1.7, 5\~7장은 전체 정리의 번호이고, 2-N은 [파트 2](tasks-part2.md), 3-N은 [파트 3](tasks-part3.md), 4-N은 [파트 4](tasks-part4.md)의 번호예요.

최종 욕설 점수는 사람이 만든 테스트셋 C, D가 있어야 낼 수 있어요. 그래서 **3-2(챌린지 모집)를 제일 먼저 시작**하고, 답이 모이는 동안 나머지를 하면 돼요.

| 번호 | 할 일 | 먼저 필요한 것 | 끝나면 누구에게 |
|---|---|---|---|
| 3-1 | 내 컴퓨터 준비, 연습 채점 | 없음 | 안 넘겨요 (막히면 파트 1) |
| 3-2 | 필터 뚫기 챌린지로 C 문장 모으기 | 3-1 | 라벨러 두 명 |
| 3-3 | D 문장 확정하기 | 3-1 | 라벨러 두 명 |
| 3-4 | 알려진 약점 예시를 D에 넣기 | 3-3 | 라벨러 두 명 |
| 3-5 | 두 사람이 따로 라벨 달기 | 3-2, 3-3, 3-4 | 정리한 시트를 3-6에서 써요 |
| 3-6 | jsonl로 바꾸고, 검사하고, PR 올리기 | 3-5 | 파트 1 (학습 데이터와 겹치는지 확인) |
| 3-7 | 파트 1이 바꾼 내 파일 읽기 | 3-1 (3-2 답을 기다리는 동안 해요) | 파트 1 (의견) |
| 3-8 | 신고 → 재학습 순서 정하기 | 3-1, 동의 문구 결정(2-6) | 파트 1, 2, 4 |
| 3-9 | A\~D와 비교군 채점하기 | 3-6, 파트 1이 모델 파일을 준 뒤 | 파트 1, 4 (점수 CSV) |

## 이미 된 것

* 우회 변형 규칙 10종이 다 있어요 (`ml/moderation/augment/__init__.py`의 `RULES`). `ml/TRAINING.md`의 "2/10종"과 [part3-moderation.md](part3-moderation.md) 2번 표("ㅅ1발 못 잡음")는 옛날 내용이에요
* 사전이 확실(`badwords.txt`), 애매(`badwords_ambiguous.txt`) 두 개로 나뉘었어요. `d35e44b`에서 엿먹어, 빙신이 애매 목록으로 옮겨졌고, 모델이 읽지 못하는 긴 메시지 뒤쪽의 애매한 말은 가려요. `badwords_soundalike.txt`도 다시 만들었어요. `464596f`에서 **나쁜 뜻뿐이면 확실, 멀쩡한 뜻(동음이의어)이 있으면 애매**라는 기준으로 다시 나눴어요. 새 단어를 넣을 때도 이 기준을 따라요. 모두 main에 있어요
* **욕설 모델 v4 학습이 끝났고 채택됐어요.** KcELECTRA를 고친 라벨, 문맥 대화, 혐오와 고인 조롱 기준으로 학습한 것(seed 123)이에요
  * 결과를 보기 전에 정한 규칙은 "세 seed 중 둘이 통과"였는데 seed 123만 통과했어요. 혐오 표현과 문맥을 더 잘 잡아서 파트 1이 채택했어요. 앞선 v3(seed 42)도 기준에 못 미친 뒤 파트 1이 정한 모델이었어요. 발표에서 물어보면 이대로 말해요
  * 서버용 파일(ONNX)로 바꿔 뒀지만 **팀이 같이 쓰는 서버에는 아직 연결하지 않았어요**
* 남은 약점: ① 문장 속 "병신년", "빙신" ② 다른 사람이 욕한 바로 다음의 멀쩡한 답장 ③ 앞 대화가 없는 강아지 "시바" ④ 나쁜 뜻뿐인 말을 인용하거나 설명하는 문장 (규칙이라 항상 가려요)
* 아직 없는 것: 사람이 만든 C셋과 D셋, 욕설 라벨 검사 도구(`eval/moderation/`에는 `score.py`만 있어요), B셋을 만드는 스크립트, 비교군을 돌리는 스크립트, 신고를 꺼내는 스크립트. 이 문서에는 스크립트 없이 하는 가장 작은 방법을 적었어요

## 모든 작업에 걸리는 규칙

1. **테스트 문장은 사람이 직접 쓴 것만** 넣어요. AI에게 만들게 하거나, 고쳐 달라거나, 비슷한 걸 더 달라고 하지 않아요
2. **테스트 문장을 AI에게 보여 주지 않아요.** 학습 데이터, 합성 프롬프트, AI 채팅 모두 안 돼요 (기획서 17.8 규칙 6). 번역기, 외부 욕설 필터 사이트에도 넣지 않아요. 시트와 파일을 열 때는 VS Code Copilot 같은 자동 완성도 꺼요
   * `score.py`는 틀린 문장을 최대 20개 화면에 찍어요. C, D 채점은 Claude Code에게 시키지 말고 **내 터미널에서 직접** 해요. 화면 캡처도 AI에 붙여 넣지 않아요
   * Claude Code의 자동 기록은 그때 바뀐 파일 내용(diff)까지 `ai-sessions/`에 남겨요. 테스트 파일을 만들고 고치고 커밋하는 동안, 그리고 테스트 파일이 들어오는 `git pull`, `git switch`를 하는 동안에는 이 레포의 Claude Code를 꺼 둬요. 다음에 켤 때는 첫 말로 "data/moderation/test_ 폴더는 열지도 검색하지도 마"라고 해요
3. **테스트 문장을 보고 필터를 고치지 않아요.** 챌린지에서 본 말을 `badwords*.txt`, `whitelist.txt`, 변형 규칙에 넣으면 시험 문제를 보고 공부한 것과 같아요. 틀린 문장을 파트 1에게 학습 아이디어로 넘기지도 않아요. 넘겨도 되는 것은 "유형별 개수"예요
4. **이 문서와 기획서에 나온 예시를 그대로 C, D에 넣지 않아요.** 이미 알려진 문장이라 시험이 되지 않아요. `sample.jsonl`의 문장도 마찬가지예요. 기획서 9.8이 테스트셋에 넣으라고 적은 두 문장(`너임마청년`, `너 임마 청년이 그러면 되냐`)만 예외예요
5. server 레포는 **누구나 볼 수 있는 공개 레포**예요. 커밋한 문장은 인터넷에 공개돼요
6. 작업마다 브랜치를 따서 작게 PR을 올려요. AI를 썼으면 `ai-sessions/part3/` 기록을 같은 PR에 넣어요 (규칙 2의 문장은 빼고요). 내 파일은 `backend/app/moderation/`, `ml/moderation/augment/`, `ml/moderation/build_train.py`, `data/moderation/`, `eval/moderation/`이고, 다른 파일은 먼저 담당 파트에 물어봐요 (server `AGENTS.md`)

명령은 macOS 터미널 기준이고 항상 `server` 폴더에서 실행해요. Windows는 Git Bash를 쓰세요 (Windows에서 돌려 보지는 못했어요). PowerShell에서는 `<<'EOF'`와 `KEY=값 명령` 모양이 동작하지 않아요. Git Bash를 열면 먼저 `export PYTHONUTF8=1`을 해요 (한글이 깨지지 않게). `make`가 없으니 3-8에 적은 대체 명령을 써요. `EOF`가 든 명령은 GitHub 화면의 코드 상자에서 복사 단추로 복사해요. 파일 원본에서 복사하면 앞 공백 때문에 마지막 `EOF`를 못 찾아요.

## 3-1. 내 컴퓨터 준비, 연습 채점

**왜**: 채점이 되는지 먼저 알아야 나중에 나온 이상한 점수가 데이터 탓인지 환경 탓인지 구분할 수 있어요.

**준비물**: git, [uv](https://docs.astral.sh/uv/), server 레포 (없으면 `git clone https://github.com/ai-class-project-2026/server.git`)

**순서**

1. 최신 코드를 받고 내 파트를 설정해요.

   ```sh
   cd server
   git switch main && git pull
   git config ai.part part3
   uv sync --group ml --group onnx
   ```

   `uv sync`는 적지 않은 그룹을 지워요. `uv sync --group ml`만 하면 모델을 돌리는 onnxruntime이 빠지니 두 그룹을 같이 적어요.

2. `.env`가 없으면 만들고 `JWT_SECRET`을 32자 이상으로 채워요. `MODERATION_ONNX_PATH=`는 비워 둬요 (비우면 사전 필터만 돌아요).

   ```sh
   cp -n .env.example .env
   uv run python -c "import secrets; print(secrets.token_urlsafe(32))"
   ```

   나온 값을 `.env`의 `JWT_SECRET=` 뒤에 붙여요.

3. 문장 하나를 판정하는 짧은 명령을 만들어요. 터미널을 새로 열 때마다 한 번 붙여 넣어요.

   ```sh
   mod() { JWT_SECRET=eval-only-placeholder-secret-0000000 uv run python -c "import sys; sys.path[:0]=['backend','ml/common']; from app.moderation import moderate; print(moderate(sys.argv[1], sys.argv[2:]))" "$@"; }
   mod 'ㅅ1발 뭐하냐'
   ```

   첫 번째가 판정할 문장이고, 그 뒤는 직전 메시지예요 (오래된 순, 최대 3개): `mod '판정할 문장' '더 옛날 메시지' '바로 앞 메시지'`

4. 커밋되어 있는 샘플을 채점해요. `--report`를 안 쓰면 `eval/reports/moderation.csv`에 점수가 쌓이니, 연습은 꼭 다른 파일에 적어요.

   ```sh
   MODERATION_ONNX_PATH= uv run python eval/moderation/score.py data/moderation/test_C_human/sample.jsonl --label try --report /tmp/part3-try.csv
   MODERATION_ONNX_PATH= uv run python eval/moderation/score.py data/moderation/test_D_fp/sample.jsonl --label try --report /tmp/part3-try.csv
   ```

**확인할 것** (모델 없이 2026-11-25에 실제로 돌린 결과)

| 해 볼 것 | 나와야 하는 것 |
|---|---|
| `mod 'ㅅ1발 뭐하냐'` | `is_profane=True`, `spans=[[0, 3]]`, `model_version='dict'` |
| `mod '시발점부터 다시 보자'` | `is_profane=False`, `spans=[]` |
| `mod '시발1가'` | `spans=[[0, 2]]` (뒤의 "1가"는 안 가려요) |
| C 샘플 채점 | `f1` 1.0, `n` 5, `false_positive_rate` null |
| D 샘플 채점 | `false_positive_rate` 0.0, `n` 4 |
| `git status` | 바뀐 파일 없음 (`eval/reports/`가 생겼으면 `--report`를 빠뜨린 거예요) |
| **일부러 망가뜨리기**: 샘플을 복사해 label을 따옴표로 감싸기 | 오류 없이 **틀린 점수**가 나와요 (아래) |

```sh
sed 's/"label": 0/"label": "0"/' data/moderation/test_D_fp/sample.jsonl > /tmp/part3-bad.jsonl
MODERATION_ONNX_PATH= uv run python eval/moderation/score.py /tmp/part3-bad.jsonl --label try --report /tmp/part3-try.csv
```

결과가 `f1` 0.0, `false_positive_rate` null이고 멀쩡한 4문장이 전부 "틀린 예시"로 나와요. `score.py`는 라벨이 숫자인지 검사하지 않아요. 그래서 3-6의 검사가 꼭 필요해요.

**끝 기준**: 위 표가 모두 같게 나와요.

**넘길 것**: 없어요.

**막히면**
* `jwt_secret ... at least 32 characters`: `.env`의 `JWT_SECRET`이 비어 있어요. `mod`를 3번 그대로 다시 붙여 넣어도 돼요
* `No such file or directory` 또는 `No module named 'app'`: `server` 폴더가 아니에요
* 하루 넘게 막히면 오류 전체를 파트 1에게 보내요

## 3-2. 필터 뚫기 챌린지로 C 문장 모으기 (100\~200개)

**왜**: C셋은 "사람이 진짜로 돌려 쓴 욕을 잡는가"를 보는 시험이에요. 지금은 샘플 5개뿐이라 기획서 4.2의 목표(C셋 F1이 기존 필터보다 +20%p)를 잴 수 없어요.

**준비물**: 구글 시트 1개, 구글 설문 1개, 참가자 15\~25명

**순서**

1. 구글 시트를 하나 만들고 첫 줄에 이 이름을 **그대로** 적어요 (3-6 명령이 이 이름으로 읽어요). C와 D가 같은 시트를 써요.

   | 열 | 이름 | 적는 것 |
   |---|---|---|
   | A | `id` | `S001`처럼 순서대로. C인지 D인지 티 나지 않게 |
   | B | `set` | `C` 또는 `D` |
   | C | `text` | 받은 문장 그대로. 띄어쓰기, 기호, 오타도 고치지 않아요 |
   | D, E, F | `ctx1`, `ctx2`, `ctx3` | 앞 메시지. 오래된 순으로 ctx1부터. 없으면 비워요 |
   | G | `meaning` | 보낸 사람이 적은 원래 말 |
   | H | `person` | `P01` 같은 참가 번호 |
   | I | `type` | 아래 유형 이름 |
   | J, K | `label_a`, `label_b` | 두 라벨러의 판정 (3-5) |
   | L | `label` | 최종 판정 (3-5) |
   | M | `use` | 쓸 것은 영어 대문자 `O`, 뺄 것은 `X` |
   | N | `note` | 뺀 이유, 논의한 내용 |

   **A\~I열과 N열을 선택해 서식 > 숫자 > 일반 텍스트**로 먼저 바꿔요. 안 하면 "18"이 숫자가 되거나 "="로 시작하는 문장이 수식이 돼요. J\~M열은 바꾸지 않아요. 일반 텍스트 칸에 쓴 계산식은 계산되지 않고 글자로 남아요. 개수를 세는 계산식은 P열 오른쪽 빈 칸에 써요. 이 시트는 나만 보고(라벨러에게는 3-5의 사본만), 엑셀로 열어 저장하지 않아요.

2. 구글 설문을 만들어요. "이메일 주소 수집"은 꺼요. 물어볼 것은 6개예요: ① "안내를 읽었고 동의해요"(필수 체크) ② 참가 번호 ③ 문장 ④ 앞에 온 메시지(선택, 최대 3개) ⑤ 원래 무슨 말인지 ⑥ 어떤 방법을 썼는지(아래 유형 + 잘 모르겠음). 설문 맨 위에 3번 글을 붙여 넣어요.

3. 참가자에게 번호(P01, P02 ...)를 하나씩 나눠 줘요. **번호와 이름을 짝지은 표는 만들지 않아요.** 모집 글은 그대로 복사해서 쓰면 돼요.

   ```text
   [필터 뚫기 챌린지] 욕설 필터를 뚫을 수 있나요?

   학교 AI 과제로 욕설 필터를 만들고 있어요. 친구끼리 채팅할 때 욕을 돌려 쓰는 문장을 직접 지어서 보내 주세요.

   - 한 사람에 10개까지, 단어 하나 말고 채팅에서 쓸 법한 한 문장으로 써 주세요.
   - 직접 쓴 문장만 보내 주세요. ChatGPT 같은 AI로 만든 것, 인터넷에서 복사한 것은 안 돼요.
   - 같은 문장에서 기호만 바꾼 것은 하나만 보내 주세요. 서로 다른 방법을 섞어 주면 좋아요.
   - 실제 사람 이름, 학교, 전화번호, 아이디를 쓰지 말고, 실제 있는 사람이나 집단을 공격하는 내용도 빼 주세요. 실제 채팅을 옮겨 적지 마세요.
   - 원래 무슨 말인지도 같이 적어 주세요. 사람이 채점할 때만 써요.

   보낸 문장은 이름 없이 공개된 과제 저장소(GitHub)에 올라가고, 필터 성능을 재는 데만 써요. 필터를 가르치는 데는 쓰지 않아요.
   빼고 싶으면 받은 참가 번호와 문장을 알려 주세요. 저장소에 올리기 전이면 흔적 없이 뺄 수 있고, 올린 뒤에는 시험지에서 빼지만 GitHub의 옛 기록에는 남을 수 있어요.
   참가는 자유이고 언제든 그만둘 수 있어요. 만 14세 미만이면 참여하지 말아 주세요.

   참가 번호: (여기에 번호)
   설문: (여기에 링크)
   ```

4. 문장을 모으는 동안 **참가자에게 필터 결과를 보여 주지 않아요.** 필터를 뚫은 것만 고르면 기존 필터 점수가 실제보다 낮아져서 비교가 불공평해져요. 잡히는 문장, 못 잡는 문장을 가리지 않고 조건에 맞으면 전부 넣어요. 유형의 예시도 주지 않아요 (그대로 따라 써요).

5. 답을 시트에 옮길 때 익명으로 바꾸고 정리해요.
   * 사람 이름, 별명, 학교, 반, 전화번호, SNS 아이디는 지어낸 이름(민수 등)으로 바꿔요. 바꾸면 우회 방법이 사라지는 문장은 빼요(`use`=X)
   * 실제 사람이나 집단을 공격하는 문장, 실제 채팅 캡처는 빼요
   * AI가 쓴 것으로 보이면 (같은 사람의 문장이 전부 비슷한 말투 등) 다시 물어보고, 확인이 안 되면 빼요
   * 뺀 문장도 지우지 말고 `use`=X, `note`에 이유만 적어요. 시트는 커밋하지 않아요

6. 중복을 정리해요.
   * 글자가 완전히 같으면 하나만 남겨요
   * 같은 사람이 기호나 숫자만 바꾼 문장은 하나만 남겨요
   * 다른 사람이 따로 쓴 비슷한 문장은 **남겨도 돼요**. "씨@발"과 "씨.발"은 서로 다른 우회예요. 자동으로 지우지 말고 눈으로 봐요
   * 욕 한 단어만 있는 문장은 빼요. 학습 데이터에 이미 많아서(예: 정규화하면 "시발" 하나가 되는 문장만 63개) 겹친다고 빠질 가능성이 커요. 또 시험지에 넣으면 다음 학습 때 같은 철자의 학습 문장이 통째로 빠져요

7. 유형을 맞춰요. 이름은 기획서 9.3 표와 똑같이 써요. `score.py`가 글자 그대로 묶기 때문에 "특수문자"와 "특수문자 삽입"이 섞이면 점수가 둘로 갈려요.

   | type | 목표 개수 |
   |---|---|
   | 숫자 치환, 특수문자 삽입, 자모 분리, 받침 분리, 초성화, 띄어쓰기 쪼개기, 유사 발음, 영문 자판/로마자, 늘이기, 복합 | 유형마다 10\~15 |
   | 기타 (위에 없는 방법) | 모인 만큼 |

   10개가 안 되는 유형은 그 유형만 더 모아요 ("받침을 떼어 쓰는 방법으로 부탁해요"처럼 방법만 알려 줘요). 끝까지 모자라면 숫자를 채우려고 직접 짓지 말고 적은 대로 두고 개수를 밝혀요.

8. 기획서 9.8의 두 문장을 넣어요 (`너임마청년`은 C, `너 임마 청년이 그러면 되냐`는 D). `person`은 `기획서`, `type`은 각각 `유사 발음`, `닮은 단어`예요. 라벨은 다른 문장과 똑같이 3-5에서 달아요.

**확인할 것**
* 한 사람 문장이 10개를 넘지 않아요: `=COUNTIF(H:H,"P01")`
* 유형별 개수: `=COUNTIFS(B:B,"C",I:I,"초성화",M:M,"O")`
* 깨 보기: `text` 열에서 찾기(Ctrl+F)로 `010`, `@`, 학교 이름, 참가자 이름을 찾아봐요. 개인정보면 고쳐요 (`@`는 우회 기호일 수도 있어요)
* 깨 보기: 아무 문장 5개를 골라 설문 원본과 글자 하나하나 비교해요. 자동 고침이나 공백이 바뀌었으면 시트 서식이 틀린 거예요

**끝 기준**: 조건에 맞는 C 후보가 100개 이상이고, 유형 10종이 있고, `person`, `meaning`, `type`이 다 채워져 있어요.

**넘길 것**: 아직 없어요. 파트 1에게는 문장을 보내지 않아요.

**막히면**: 사람이 안 모이면 목표를 100개로 잡고 유형을 먼저 채워요. 넣어도 되는지 애매한 문장은 `note`에 적고 3-5에서 같이 정해요.

## 3-3. D 문장 확정하기 (100개)

**왜**: D셋은 멀쩡한 말을 욕으로 잡는 비율(오탐률)을 재요. 목표는 3% 이하(100개 중 3개까지)예요. 지금 있는 `data/moderation/test_D_fp/draft_v1.jsonl`은 **AI가 만든 초안**이고 사람이 검수하지 않았어요 (기록: `ai-sessions/part1/2026-11-23_claude_training-prep.md`의 20번).

**준비물**: 3-2의 시트, `draft_v1.jsonl`(텍스트 편집기로 열어요. AI로 열지 않아요)

**순서**

1. 초안 100줄을 보고 **유형 이름만** 시트 밖에 적어 둬요. 초안은 어떤 상황을 다룰지 보는 참고용이에요.
2. 유형마다 사람이 자기 말로 새 문장을 써요. 초안 문장을 단어 몇 개만 바꿔서 쓰지 않아요. 나와 다른 팀원(파트 1 말고) 두 사람 이상이 나눠 쓰고, 챌린지 참가자에게 "욕처럼 보이지만 멀쩡한 문장"도 부탁해요. 내가 3-2처럼 익명으로 바꿔 시트에 `set`=`D`로 넣고 `person`을 적어요. 라벨러(3-5)가 쓴 문장이 절반을 넘지 않게 해요. 자기가 쓴 문장은 무조건 0으로 달기 쉬워요. `meaning`에는 "왜 욕처럼 보이는지"를 적어요.
3. 유형은 이걸 기본으로 하고 유형마다 10개쯤 만들어요.

   | type | 무엇 |
   |---|---|
   | 닮은 단어 | 글자만 욕과 비슷한 낱말 |
   | 숫자 | 나이, 학년, 시각처럼 18 등이 든 말 |
   | 동물·사물 | 짐승 새끼, 물건이 꺼지는 것처럼 멀쩡한 뜻 |
   | 띄어쓰기 | 공백을 지우면 욕이 되는 말 |
   | 초성·줄임말 | 채팅 초성 (ㄱㄱ, ㅇㅋ 등) |
   | 영문·자판 | 영어 단어, 영타로 친 멀쩡한 말, 한 글자 섞임 |
   | 칭찬·감탄 | 존맛, 존예처럼 칭찬으로 쓴 말 (팀 기준으로 욕 아님) |
   | 거칠지만 욕 아님 | 1.4 표에서 욕 아님으로 정한 말 (놈, 쓰레기, 뒤지다, 열라) |
   | 일상 | 욕과 전혀 상관없는 평범한 채팅 |
   | 문맥 있음 | 앞 메시지를 봐야 멀쩡한 뜻인 말 |

   "ㅇㅋ", "ㅋㅋ"처럼 누구나 쓰는 짧은 말만으로 된 문장은 넣지 않아요. 다음에 학습 데이터를 만들 때 똑같은 말이 본문이나 문맥에 든 줄이 전부 빠져요 (`build_train.py`의 `held_out`). 3-4의 답장도 마찬가지예요.

4. 초안 문장을 한 글자도 안 바꾸고 남기고 싶은 것이 있으면 `note`에 "초안"이라고 적어요. PR에 몇 개인지 밝히고, 남길지는 파트 1과 정해요 (테스트셋은 사람이 쓴 문장만 쓰는 게 원칙이에요).
5. `draft_v1.jsonl`은 지우거나 덮어쓰지 않아요. `build_train.py`가 `test_D_fp` 폴더의 모든 `.jsonl`을 학습에서 빼기 때문에 그냥 두는 게 안전해요.

**확인할 것**
* 욕 단어를 인용한 문장은 D가 아니에요 ("그 단어를 언급만 해도 보이면 욕"이 기준이에요, 3-5)
* 깨 보기: 만든 문장을 소리 내어 읽어 보고, 다른 사람에게 이유를 말하지 않고 "가려야 할 것 같아?"라고 물어봐요. 그렇다고 하면 유형이나 문장을 다시 봐요
* 라벨과 `use`가 정해질 때까지 `mod`나 `score.py`에 **넣어 보지 않아요**. 결과를 보면 통과하는(또는 걸리는) 문장만 고르게 돼요

**끝 기준**: `set`=D, `use`=O 후보가 100개 이상(약점 문장 빼고), 유형마다 5개 이상.

**막히면**: 문장이 안 떠오르면 그 유형의 상황을 참가자에게 말로만 설명하고 써 달라고 해요.

## 3-4. 알려진 약점 예시를 D에 넣기

**왜**: 약점을 숫자로 솔직하게 보여 주고 다음 개선 방향을 말하려고요. 전부 **틀릴 가능성이 큰 문장**이라 개수와 계산법을 점수 내기 전에 정해야 해요.

**준비물**: 3-3의 시트

**순서**

1. 약점마다 같은 개수(5\~8개)를 사람이 써요. 전부 label 0이에요.

   | type (이 이름 그대로) | 무엇을 쓰나 | ctx |
   |---|---|---|
   | `약점: 해 이름` | 육십갑자 해 이름 "병신년"이 든 문장 | 반은 비우고, 반은 해 이름이라는 게 드러나는 앞 메시지 |
   | `약점: 이름 빙신` | 게임이나 만화 속 이름 등으로 쓴 "빙신" | 반은 비우고, 반은 넣기 |
   | `약점: 욕 다음 답장` | 다른 사람이 욕한 다음에 온 멀쩡한 답장 (답장 자체에는 욕이 없어요) | 꼭 넣기. 마지막 앞 메시지가 욕이에요 |
   | `약점: 시바` | 강아지 뜻 "시바"가 든 문장 | 반은 비우고, 반은 강아지 이야기 |

2. 기본 D 100개와 약점 문장의 개수를 PR에 따로 적어요.
3. 파트 1과 **채점 전에** 정해요: 목표(오탐률 3%)를 D 전체로 볼지, 약점을 뺀 100개로 볼지. 추천은 둘 다 적고 전체를 먼저 보여 주는 거예요. 점수를 본 뒤에 빼고 싶은 문장을 빼면 안 돼요.

**확인할 것** (모델이 있을 때와 없을 때. v4 모델로 2026-11-25에 확인한 값)

| 넣을 것 | 모델 없음 | v4 | 뜻 |
|---|---|---|---|
| `mod '병신년'` | True, `dict` | True, 모델 | "병신"은 동음이의어라 모델이 판단해요. 앞 메시지가 없으면 욕으로 봐요 |
| `mod '병신년' '올해가 무슨 해야?'` | True, `dict` | False (0.0015), 모델 | 앞 대화가 있으면 풀려요 |
| `mod '병신년' '육십갑자 해 이름 적어 줘'` | True, `dict` | False (0.0001), 모델 | 같은 이유예요 |
| `mod '빙신'` | True, `dict` | True (0.99), 모델 | 앞 메시지가 없으면 욕으로 봐요 (v3는 통과시켰어요) |
| `mod '맘충은 왜 혐오 표현일까'` | True, `dict` | True, `dict` | 나쁜 뜻뿐인 말이라 인용해도 가려요 (정한 규칙이에요) |
| `mod '시바'` | True, `dict` | True, 모델 | 앞 메시지가 없으면 욕으로 봐요 |
| `mod '시바' '강아지 종이 뭐야?'` | True, `dict` | False, 모델 | 문맥이 있으면 풀려요. 다른 문장에서는 틀릴 수 있어요 |
| `mod '청년' '병신아'` | False, `dict` | **True, spans=[]** | 멀쩡한 말인데 앞 메시지 때문에 욕이 돼요. 가릴 위치가 없는데도 욕이라고 나와요 |

v4 파일이 없으면 왼쪽 칸만 확인하고, 오른쪽은 3-9에서 봐요.

**끝 기준**: 약점 4종이 각각 5개 이상 `use`=O로 있고, 3번의 결정이 글로 남아 있어요.

**넘길 것**: 3-6의 PR에 같이.

**막히면**: 앞 메시지의 욕은 짧고 흔한 말 하나면 충분해요. 특정 사람을 욕하는 말은 쓰지 않아요.

## 3-5. 두 사람이 따로 라벨 달기

**왜**: 한 사람 생각으로 단 라벨은 흔들려요. 기획서 13장이 2인 교차 라벨링을 정했고, 학습 라벨과 같은 기준이어야 점수가 공정해요.

**준비물**: 정리된 시트, 라벨러 2명, 아래 기준표. 나는 모으면서 `meaning`과 `type`을 이미 봤으니 **파트 2와 파트 4가 다는 게 제일 좋아요**. 내가 라벨러를 하면 그 사실을 PR에 적어요. 파트 1은 모델을 학습시키는 사람이라 빼요

**순서**

1. 라벨러마다 **파일 > 사본 만들기**로 시트를 따로 만들고, `use`=X인 행과 `id`, `text`, `ctx1`\~`ctx3` 말고 다른 열을 모두 지워요 (탭만 복사하면 옆 탭의 `set`, `meaning`이 보여요). 머리글을 뺀 전체를 선택해 순서를 섞고(데이터 > 범위 무작위 섞기) 그 라벨러에게만 공유해요. C와 D를 같이 섞어야 "이건 C니까 1" 하고 달지 않아요.
2. 두 사람이 서로 보지 않고 F열에 `1`, `0`, `u`(모르겠음)를 달아요. 판정하는 것은 `text`뿐이에요. 앞 메시지는 뜻을 알 때만 써요. 하루에 다 하지 말고 50개씩 나눠도 돼요. 불쾌하면 쉬거나 바꿔도 돼요.
3. 두 사본을 원래 시트의 새 탭 `A탭`, `B탭`에 붙여 넣고, `id`로 찾아서 옮겨요. 순서를 섞었으니 열을 그대로 복사하면 안 돼요. J2에 `=VLOOKUP(A2,'A탭'!A:F,6,FALSE)`, K2에 `'B탭'`으로 적고 아래로 채워요. `#N/A`는 `use`=X라서 라벨러에게 안 준 행이니 지우고(다른 행이면 `id`가 안 맞는 거예요), 나머지는 복사해서 **값으로** 붙여 넣어요.
4. 합의하기 **전에** 일치율을 남겨요: `=SUMPRODUCT((J2:J<>"")*(J2:J&""=K2:K&""))/COUNTA(J2:J)`. 개수와 일치율은 PR에 적어요.
5. 다른 것만 만나서 얘기해요. 이때부터 `meaning`을 봐요. 그래도 안 정해지거나 둘 다 `u`면 `use`=X, `note`="기준 없음"이에요. 기준이 없는 유형은 모아서 파트 1에게 **유형 설명으로만** 알려요 (문장은 안 보내요).
6. 최종 `label`을 채워요. 규칙은 이래요.
   * C 후보: 최종 1이면 C에 남겨요. 최종 0이면, 욕과 닮았을 뿐인 멀쩡한 말은 `set`을 `D`로 바꾸고 `type`도 D 유형으로 고쳐요. 비꼼이나 암시처럼 복원할 욕이 없는 문장은 `use`=X (9.8: 자동 판정 제외)
   * D 후보: 최종 0만 남기고, 1로 나오면 `use`=X
   * 그래서 C는 label 1만, D는 label 0만 있게 돼요

라벨 기준은 **기획서 9.8 + 1.4 표**예요. 파트 1이 학습 라벨을 다시 달 때 1.4에 빠져 있던 것도 함께 쓰니 시험지도 똑같이 해요.

| 상황 | 라벨 |
|---|---|
| 특정 욕으로 복원되고 그 뜻으로 씀 (우회 표기도 같음) | 1 |
| 1.4 표의 판정 | 그 표 그대로 |
| 애매 목록으로 옮긴 말(엿먹어, 빙신 등)도 욕 뜻이면 | 1 |
| 욕 단어를 인용하거나 언급만 해도 그 단어가 그대로 보임 | 1 |
| 존맛, 존예, 존멋, 존잼을 칭찬으로 씀 | 0 |
| 감탄의 "미친", "개-" 강조("개웃겨") / 사람에게 쓴 "미친놈" | 0 / 1 |
| 글자만 같은 다른 낱말 (해 이름, 사람 이름, 외국어, 오타) | 0 |
| 앞 메시지에만 욕이 있고 `text`는 멀쩡함 | 0 |
| 복원할 욕이 없는 비꼼, 암시, 무례함 | 0 (C에서는 빼요) |

**확인할 것**
* `=COUNTIFS(M:M,"O",L:L,"")`가 0이에요 (라벨이 빈 행 없음). C인데 0이거나 D인데 1인 행은 3-6의 2번 명령이 잡아요
* 깨 보기: 일치율이 100%면 의심해요. 서로 보고 달지 않았는지, C와 D를 안 섞지 않았는지 확인해요
* 깨 보기: 최종 라벨이 정해진 문장 10개를 다시 안 보이게 골라 한 번 더 달아 봐요. 2개 넘게 바뀌면 기준 얘기를 먼저 해요

**끝 기준**: `use`=O인 모든 행에 `label`이 있고, 일치율과 "기준 없음" 개수를 적어 뒀어요.

**넘길 것**: 일치율, 빠진 개수와 이유(문장 없이).

**막히면**: 기준에 없는 유형이면 `u`로 두고 넘어가요. 억지로 정하지 않아요.

## 3-6. jsonl로 바꾸고, 검사하고, PR 올리기

**왜**: `score.py`는 형식이 틀려도 조용히 잘못된 점수를 낼 수 있어요. 욕설용 라벨 검사 도구는 없어서 `build_train.py`의 검사 함수(`check_row`, `surface`)를 빌려 써요. (일정은 `eval/schedule/check_labels.py`가 있지만 욕설에는 못 써요.)

**준비물**: 라벨이 끝난 시트, **Claude Code를 끈** 터미널

**순서**

1. 원래 시트 탭을 연 채로 **파일 > 다운로드 > 쉼표로 구분된 값(.csv)**을 받아요. 레포 밖 (예: `~/Downloads/moderation_sheet.csv`)에 둬요. CSV에는 `person`, `meaning`이 들어 있으니 커밋하지 않아요.
   브랜치는 **한 번만** 만들어요. 다시 할 때는 건너뛰어요.

   ```sh
   git switch main && git pull && git switch -c data/test-cd
   ```

2. 바꿔요. `use`가 `O`, `X`가 아니거나 `set`이 `C`, `D`가 아니거나(소문자, 뒤 공백도), `id`가 겹치거나, C인데 label이 1이 아니거나 D인데 0이 아니면 멈춰요.

   ```sh
   uv run python - ~/Downloads/moderation_sheet.csv <<'EOF'
   import csv, json, sys
   from pathlib import Path
   WANT = {"C": ("1", "data/moderation/test_C_human/test.jsonl"), "D": ("0", "data/moderation/test_D_fp/test.jsonl")}
   def clean(s):  # 폰에서 복사할 때 섞이는 특수 줄바꿈을 보통 줄바꿈으로 (그대로 두면 jsonl 한 줄이 둘로 갈려요)
       return "\n".join(s.strip().splitlines())
   with open(sys.argv[1], encoding="utf-8-sig", newline="") as f:
       rows = [r for r in csv.DictReader(f) if r["id"].strip() or r["text"].strip()]
   ids = [r["id"] for r in rows]
   for r in rows:
       if r["use"] not in ("O", "X") or r["set"] not in WANT or ids.count(r["id"]) > 1:
           sys.exit(f"{r['id']}: use는 O나 X, set은 C나 D, id는 겹치지 않게 (빈칸, 소문자, 뒤 공백도 확인)")
   rows = [r for r in rows if r["use"] == "O"]
   for name, (want, path) in WANT.items():
       out = []
       for r in rows:
           if r["set"] != name:
               continue
           if r["label"] != want or not r["text"].strip() or not r["type"].strip():
               sys.exit(f"{r['id']}: {name}셋은 label이 {want}이고 text, type이 있어야 해요. 시트를 고치고 다시 하세요")
           row = {"text": clean(r["text"]), "label": int(want), "type": r["type"].strip()}
           if ctx := [clean(c) for c in (r["ctx1"], r["ctx2"], r["ctx3"]) if c.strip()]:
               row["context"] = ctx
           out.append(json.dumps(row, ensure_ascii=False) + "\n")
       Path(path).write_text("".join(out), encoding="utf-8")
       print(path, len(out), "줄")
   EOF
   ```

   한 줄은 `{"text": "...", "label": 1, "type": "초성화", "context": ["이전 메시지", ...]}` 모양이고 `context`는 있을 때만 들어가요. 위 명령은 `person`과 `meaning`을 버려요.

3. 검사해요. 문장은 화면에 안 나오고 줄 번호만 나와요.

   ```sh
   uv run python - <<'EOF'
   import sys
   sys.path[:0] = ["ml/moderation"]
   from collections import Counter, defaultdict
   from pathlib import Path
   from build_train import check_row, read_jsonl, surface
   seen = defaultdict(list)
   for path in ("data/moderation/test_C_human/test.jsonl", "data/moderation/test_D_fp/test.jsonl"):
       rows = list(read_jsonl(Path(path)))
       print(path, len(rows), "줄, label", dict(Counter(r.get("label") for _, r in rows)))
       print("  type:", dict(Counter(r.get("type", "(없음)") for _, r in rows)))
       for n, r in rows:
           if why := check_row(r):
               print(f"  고칠 줄 {n}: {why}")
           elif len(r.get("context", [])) > 3:
               print(f"  고칠 줄 {n}: context가 4개 이상")
           else:
               seen[surface(r["text"]), tuple(r.get("context", []))].append(f"{path.split('/')[2]}:{n}")
   print("비슷해서 볼 줄:", [v for v in seen.values() if len(v) > 1] or "없음")
   EOF
   ```

   "비슷해서 볼 줄"은 공백과 기호를 빼면 같은 문장이에요. 3-2의 6번 기준으로 눈으로 보고, 실수로 겹친 것만 시트에서 `use`=X로 바꾼 뒤 CSV를 새로 받아 2번부터 다시 해요. `type:`에 "숫자 치환"과 "숫자치환"처럼 비슷한 이름이 둘 보이면 시트에서 하나로 맞춰요. 두 파일의 줄 수가 시트의 개수(`=COUNTIFS(B:B,"C",M:M,"O")`)와 같은지도 봐요. **test.jsonl을 손으로 고치지 않아요** (시트와 달라져요).

4. 사전 필터만으로 한 번 채점해서 파일이 채점기에서 돌아가는지 봐요. 이건 연습이라 다른 CSV에 적어요.

   ```sh
   MODERATION_ONNX_PATH= uv run python eval/moderation/score.py data/moderation/test_C_human/test.jsonl --label try --report /tmp/part3-try.csv
   MODERATION_ONNX_PATH= uv run python eval/moderation/score.py data/moderation/test_D_fp/test.jsonl --label try --report /tmp/part3-try.csv
   ```

   점수가 낮아도 **문장을 고치지 않아요.** 라벨이 잘못된 걸 찾았을 때만 시트에서 두 사람이 다시 정하고, 바꾼 이유를 PR에 적어요.

5. 커밋하고 PR을 올려요.

   ```sh
   git status
   git add data/moderation/test_C_human/test.jsonl data/moderation/test_D_fp/test.jsonl
   git commit -m "Add human test sets C and D"
   git push -u origin data/test-cd
   ```

   첫 줄의 `On branch`가 `data/test-cd`여야 해요. `git status`에는 `test.jsonl` 두 개만 보여야 해요. CSV나 `ai-sessions/` 파일이 보이면 왜 생겼는지 먼저 확인해요.

   PR 설명에는 문장 없이 이것만 적어요: 건수(C, D 기본, D 약점), 욕/정상 수, `type`별 건수, 참가자 수, 1인당 최대 개수, 두 사람 일치율, 뺀 개수와 이유, 초안 문장을 그대로 남긴 개수, 채점 도구가 돈 것을 확인했다는 한 줄. 2번과 3번의 명령과 출력도 붙여요 (줄 번호만 나와요).

6. 문장 내용은 라벨러가 PR에서 봐요. 파트 1은 형식과 건수를 보고 스크립트로 **학습 데이터와 겹치는 문장**을 확인해요 (점수를 내기 전에). 파트 1이 겹치는 줄 번호를 알려 주면 시트에서 빼고 같은 순서로 다시 올려요. 이미 학습한 문장으로 점수를 내면 안 되니까요.

**확인할 것** (샘플 문장으로 미리 해 봤어요)
* 정상일 때: 검사 결과에 "고칠 줄"이 없고 C의 label은 `{1: ...}`, D는 `{0: ...}`만 나와요
* 깨 보기: 3번을 하기 전에 D 파일 끝에 아무 줄이나 `x{`를 붙이면 `skip ...: not valid JSON`이 나와야 해요. `label`을 `"0"`으로 바꾸면 `label must be 0 or 1`이 나와야 해요. 확인했으면 2번 명령을 다시 돌려 원래대로 돌려요
* 깨 보기: 받은 CSV의 사본에서 C 한 줄의 `label`을 0으로, 다른 줄의 `use`를 `o`로 바꿔 2번에 넣으면 각각 `S0xx: C셋은 label이 1이고 ...`, `S0xx: use는 O나 X ...`라고 멈춰야 해요
* 엑셀로 저장한 CSV를 쓰면 맨 앞에 보이지 않는 글자(BOM)가 붙을 수 있어요. 2번 명령은 처리하지만, 직접 만든 jsonl에 BOM이 있으면 `score.py`가 `Unexpected UTF-8 BOM`으로 멈추고, `build_train.py`는 **그 첫 문장을 시험지에서 빼먹어요**. 그래서 jsonl을 손으로 만들지 않아요
* `context`를 글자 하나(`"안녕"`)로 적으면 오류 없이 글자 단위로 잘려요. 3번 검사가 `context must be a list of strings`로 잡아요

**끝 기준**: `test.jsonl` 두 개가 main에 들어갔고, 검사 출력이 PR에 있고, 파트 1이 겹침 확인을 마쳤어요.

**넘길 것**: PR 주소와 건수를 파트 1에게. **최종 평가 전에.** 문장을 메신저로 보내지 않아요.

**막히면**
* `KeyError: 'use'`: 시트 첫 줄의 열 이름이 다르거나 엉뚱한 탭을 받았어요
* `No such file or directory`: CSV 경로를 확인해요
* `KeyError: 'label'`이 `score.py`에서 나오면 `label`이 빠진 줄이 있어요. 3번 검사를 먼저 해요

## 3-7. 파트 1이 바꾼 내 파일 읽기

**왜**: 최종 평가는 이 코드로 돌아가요. 내 파일인데 설명을 못 하면 다시 짜야 해요 (기획서 17.8 규칙 5).

**준비물**: 3-1의 `mod`. 모델이 있으면 더 좋아요(3-9의 1번). 없으면 "모델 없음" 칸만 봐요

**순서**

1. 바뀐 파일만 봐요. `d44db1f`는 크니까 파일마다 나눠서 봐요.

   ```sh
   git show d44db1f -- backend/app/moderation/__init__.py backend/app/moderation/candidate.py
   git show d44db1f -- backend/app/moderation/normalize.py backend/app/moderation/dict_filter.py
   git show d44db1f -- ml/moderation/build_train.py eval/moderation/score.py
   git show 3da940b e090259 d35e44b 5db5038
   ```

2. 지금 코드는 `cat -n backend/app/moderation/__init__.py`처럼 열어서 읽어요. 읽는 순서는 `normalize.py` 맨 위 설명 → `dict_filter.py` → `candidate.py` → `classifier.py` → `__init__.py`의 `moderate()` → `build_train.py` 맨 위 설명이에요.
3. 아래 질문을 **답부터 적고** 실행해서 맞는지 봐요. "모델 없음"은 `MODERATION_ONNX_PATH=`로 시작하고, "v4"는 `MODERATION_ONNX_PATH=모델폴더`로 시작해요 (예: `MODERATION_ONNX_PATH= mod '존맛'`).

   | 질문 | 실행 | 맞는 답 |
   |---|---|---|
   | 1. 확실한 욕과 애매한 말은 어디서 갈리나요? | `mod '시1발'`, `mod '시 발'` | 둘 다 모델 없으면 True, `dict`. 코드로는 앞의 것이 `certain`, 뒤의 것이 `ambiguous`예요 (`candidate.default_hits`: 원문에서 공백으로 떨어져 있으면 애매). v4면 앞은 `dict`(모델을 안 봐요), 뒤는 `moderation-v4-onnx` |
   | 2. 모델이 없을 때 애매한 말은? | `mod '존맛'` | 모델 없음 True, `dict`. v4는 False |
   | 3. 정규화가 원문을 어떻게 바꾸나요? | `mod '시발1가'`, `mod '게 새끼네'` | `restored`가 `시발가`, `게새끼네`. `spans`는 원문 위치 `[[0, 2]]`, `[[2, 4]]` |
   | 4. "ㅅ1발"은 왜 `restored`가 그대로 `ㅅ1발`인가요? | `mod 'ㅅ1발 뭐하냐'` | 기본 읽기는 없는 글자를 지어내지 않아요. 후보 읽기(`candidate.hits`)에서만 걸려서 가리지만, 복원 글자는 기본 읽기 것이에요. **기획서 9.1(복원형 "시발")과 다르니** 의견이 있으면 적어요 |
   | 5. 애매한 말도 문맥 없이 긴 글 뒤에 있으면? | `mod "$(printf '회의 자료 정리해서 올려 주세요 %.0s' {1..15})존맛"` | True, `dict`, `spans=[[270, 272]]`. 모델은 128토큰만 읽어서 뒤를 못 봐요 (`d35e44b`의 `_seen_end`). v4도 True예요. 앞의 2번과 반대 결과예요 |
   | 6. 사전에 없는 우회는? | `mod '너임마청년'`, `mod '너 임마 청년이 그러면 되냐'` | 모델 없음은 둘 다 False. v4도 앞의 것을 못 잡아요 (False). 기획서 9.8의 예시인데 아직 놓쳐요 |
   | 7. 공백을 지워 생긴 욕은? | `mod '수박 씨 발아율'` | 모델 없음 True(`spans=[[3, 6]]`), v4도 True (0.99, 오탐). v3는 False였어요. 사전이 애매로 보낸 것을 모델이 정해요 |
   | 8. 앞 메시지가 판정을 바꾸나요? | `mod '청년'`과 `mod '청년' '병신아'` | 모델 없음은 둘 다 False. v4는 뒤의 것이 True인데 `spans`가 비어 있어요 (3-4의 약점) |
   | 9. 파일 이름을 잘못 적으면? | `MODERATION_ONNX_PATH=/없는폴더 mod '존맛'` | 오류로 멈추지 않고 경고 한 줄과 함께 `dict`로 돌아가요. 점수를 잴 때 위험해요 (3-9) |
   | 10. `build_train.py --help`의 새 옵션 3개는? | `uv run python ml/moderation/build_train.py --help` | `--fixes`(라벨 덮어쓰기), `--hold-out`(더 뺄 문장), `--out`(저장 위치) |
   | 11. 변형이 욕을 새로 만들면 안 되는 이유는? | 아래 명령 | `False False True` (멀쩡한 문장에 욕을 만들거나 욕 문장을 멀쩡한 말로 바꾸면 버려요. "병@신"과 "병신"은 같은 문장 가족이에요) |

   ```sh
   uv run python -c "import sys; sys.path[:0]=['ml/moderation']; from build_train import key, label_safe; print(label_safe('장녀','창녀',0), label_safe('창녀','장녀',1), key('병@신')==key('병신'))"
   ```

4. 답이 다르면 먼저 코드에서 이유를 찾아요. 그래도 이상하면 "이 입력을 넣으니 이렇게 나왔고 이래야 한다고 생각한다"로 적어요. 테스트 문장은 예로 쓰지 않아요.
5. 사전이나 정규화를 고치고 싶으면 **테스트셋을 보기 전에** 이유와 (테스트셋이 아닌) 예시로 제안하고, PR에 전/후 채점(A셋 등, C/D 말고)을 붙여요. C/D를 본 뒤에는 그 문장 때문에 고치지 않아요.

**확인할 것**
* 11개 질문에서 답을 보지 않고 9개 이상 맞히고, 틀린 것은 왜 틀렸는지 코드 줄을 가리켜 설명할 수 있어요
* 깨 보기: 아무 욕이나 하나 골라 글자 사이에 공백 1개, 공백 2개, 이모지, 줄바꿈을 넣어 `mod`로 돌려 보고 어디서 놓치는지 기록해요 (이때 쓴 문장은 C에 넣지 않아요)

**끝 기준**: 질문 답안, 이상한 점, 반대 의견(이유와 예시)을 적은 글을 PR이나 파트 1에게 보냈어요.

**넘길 것**: 의견. 최종 평가 전에.

**막히면**: 한 파일에 30분 넘게 걸리면 질문 번호를 적어 파트 1에게 물어봐요.

## 3-8. 신고 → 재학습 순서 정하기 (기획서 9.7)

**왜**: 발표 마무리가 "신고 → 재학습으로 계속 좋아지는 구조"예요 (기획서 15장 5번). 말만이 아니라 한 번은 해 봐야 해요.

**준비물**: 3-1 환경, 테스트 계정(`make seed`가 만들어요), 동의 문구 결정(2-6). **결정 전에는 실제 사용자 메시지를 쓰지 않아요.** 지금 화면에는 "AI 학습에 쓰지 않아요"라고 적혀 있고(`components/app/consent.ts`), 기획서 11장은 "동의한 경우 익명화해서 사용"이라고 해서 서로 달라요. **2026-11-25에 "따로 동의받은 경우만 익명으로 씀"으로 정했지만**, 동의를 받는 기능이 아직 없으니 만들기 전에는 테스트 계정으로만 해 봐요

**순서**

1. 지금 있는 것과 없는 것을 확인해요.
   * 있음: `POST /reports`(`false_positive` 오탐, `missed` 놓침)와 `reports` 테이블. 같은 사람이 같은 메시지를 같은 종류로 또 신고하면 같은 신고 번호가 돌아와요. 채팅 화면의 [욕설 아님]과 깃발 버튼이 이걸 불러요
   * 없음: 신고 목록을 보는 API, 꺼내는 스크립트, 30일 뒤 자동 삭제, 운영자 화면
2. 테스트용 서버에서 신고를 넣어 봐요. 내 컴퓨터에서만 하고, 공용 서버에서 하지 않아요.

   ```sh
   make migrate && make seed
   make dev
   ```

   Git Bash에는 `make`가 없으니 이렇게 해요.

   ```sh
   export PYTHONPATH='backend;ml/common'
   uv run alembic upgrade head && uv run python backend/seed.py
   ALLOW_OPEN_AI_API=1 uv run uvicorn app.main:app --app-dir backend
   ```

   `make dev`는 켜 두고, 아래는 새 터미널에서 해요. 채널 번호는 `make seed`를 새 `app.db`에 했을 때 1이에요.

   웹으로 해도 되고(파트 2에게 물어보기), 이렇게 해도 돼요. 메시지 번호는 응답의 `id`예요.

   ```sh
   TOKEN=$(curl -s localhost:8000/auth/login -H 'Content-Type: application/json' -d '{"email":"minjun@demo.com","password":"demo1234"}' | uv run python -c "import json,sys; print(json.load(sys.stdin)['access_token'])")
   curl -s localhost:8000/channels/1/messages -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' -d '{"text":"게 새끼네"}'
   curl -s localhost:8000/reports -H "Authorization: Bearer $TOKEN" -H 'Content-Type: application/json' -d '{"message_id":여기에_id,"type":"false_positive"}'
   ```

   Windows에서 한글이 깨지면 http://localhost:8000/docs 화면의 `POST /auth/login`에서 토큰을 받아 Authorize에 넣고 같은 요청을 보내요.

3. 신고를 꺼내요. 실제 운영용 스크립트는 없으니 파트 1에게 요청하고, 연습은 내 `app.db`에서 읽기만 해요. 신고한 사람 번호는 뺐어요.

   ```sh
   uv run python - <<'EOF'
   import csv, sqlite3
   con = sqlite3.connect("file:app.db?mode=ro", uri=True)
   with open("/tmp/reports_review.csv", "w", newline="", encoding="utf-8-sig") as f:
       w = csv.writer(f)
       w.writerow(["report", "type", "masked", "model", "text", "ctx1", "ctx2", "ctx3", "label_a", "label_b", "label", "use"])
       for rid, kind, masked, model, text, ch, mid in con.execute(
           "SELECT r.id, r.type, m.is_profane, m.model_version, m.text, m.channel_id, m.id "
           "FROM reports r JOIN messages m ON m.id = r.message_id ORDER BY r.id"
       ).fetchall():
           ctx = [t for (t,) in con.execute(
               "SELECT text FROM messages WHERE channel_id = ? AND id < ? ORDER BY id DESC LIMIT 3", (ch, mid))][::-1]
           w.writerow([rid, kind, masked, model, text, *ctx, *[""] * (3 - len(ctx)), "", "", "", ""])
   EOF
   ```

   Git Bash에서 `/tmp`를 못 찾으면 `~/Downloads/reports_review.csv`로 바꿔요. 앞 메시지를 같이 꺼내는 이유는 서버가 판정할 때 쓰는 문맥이 직전 3개이기 때문이에요. **앞 메시지는 신고한 사람이 아니라 다른 사람이 쓴 말**이에요. 실제 데이터라면 그 사람의 동의도 필요하고, 없으면 `ctx`를 비워요.
4. 순서를 정해서 글로 적어요. 적을 곳은 `data/README.md`이고 아래를 채워요.

   | 단계 | 누가 | 어떻게 |
   |---|---|---|
   | ① 모으기 | 파트 1 | 동의한 사람의 신고만 (문맥은 쓴 사람도 동의했을 때만) 3번 방식으로 꺼내고, 이름과 개인정보를 지우고 보내요 |
   | ② 검수 | 파트 3 + 다른 한 명 | 3-5와 같은 기준으로 따로 달고, 다르면 같이 정해요. 같은 메시지에 오탐과 놓침이 둘 다 오면 하나만 남기고, 안 정해지면 버려요 |
   | ③ 시험지와 겹치는지 | 파트 3 | C/D와 같은 문장이면 학습 데이터에 넣지 않아요 (`build_train.py`도 `test_C_human`, `test_D_fp`, `eval_only`의 문장과 철자만 다른 문장을 자동으로 빼요) |
   | ④ 넣기 | 파트 1 | 검수한 문장을 `data/moderation/raw/reports_reviewed.jsonl`에 `{"text", "label", "context"}` 한 줄씩 저장 (gitignore). `uv run python ml/moderation/build_train.py --fixes ...`로 다시 만들어요. 라벨 수정 파일은 git에 없으니 파트 1 컴퓨터에서 해요 |
   | ⑤ 재학습 | 파트 1 | 시작하기 **전에** 기준을 적어요. `--val-groups`로 검증용 문장 묶음을 고정하고 `--seed`를 2개 이상 써요 |
   | ⑥ 재평가 | 파트 3 | 3-9와 같은 방법으로 A\~D를 다시 채점해요 |
   | ⑦ 배포 | 파트 1 | 기준을 다 통과했을 때만. 아니면 지금 모델을 그대로 둬요 |

5. ⑤의 기준을 채워요. 숫자는 점수를 보기 전에 파트 1과 정해서 `data/README.md`에 적어요: C셋 F1(더 낮지 않을 것), D셋 오탐률(지금보다 올라가지 않고 목표 3%), A셋 F1(얼마까지 떨어져도 되는지), 약점 유형별로 나빠진 것이 없는지, seed 두 개가 모두 통과할 것. v3가 여기서 배운 예예요 (seed 42 통과, seed 7 하나 탈락으로 보류 → 사람이 결정).
6. 테스트 계정으로 신고 3\~5건을 넣고 ①\~③을 실제로 해 봐요. 재학습은 하지 않아도 돼요.

**확인할 것** (2026-11-25에 임시 서버에서 확인)
* 같은 신고를 두 번 보내면 두 번 다 201이고 번호가 같아요. 오탐과 놓침을 같은 메시지에 둘 다 넣으면 **둘 다 저장돼요** (모순이라 ②에서 처리)
* 깨 보기: `message_id`에 없는 번호를 넣으면 404, `type`에 다른 글자를 넣으면 422
* 깨 보기: 채널의 첫 메시지(`make seed`면 1번)를 신고해서 3번 CSV의 `ctx`가 비어 있는지 봐요
* 꺼낸 CSV에 이름, 이메일, 사용자 번호 열이 없어요

**끝 기준**: `data/README.md`에 순서와 기준이 적힌 PR이 있고, 연습한 신고 건수와 검수 결과(건수만)가 PR에 적혀 있어요.

**넘길 것**: PR을 파트 1, 2에게, 발표에 쓸 한 장짜리 순서를 파트 4에게.

**막히면**: `no such table: reports`면 `make migrate`를 안 한 거예요. 로그인이 안 되면 `make seed`가 먼저예요. 8000번이 이미 쓰이면 `make dev`를 켠 터미널을 확인해요.

## 3-9. A\~D와 비교군 채점하기 (모델을 받은 뒤)

**왜**: 기획서 4.2의 목표는 사람 테스트셋으로만 판정해요. 점수는 파트 4가 발표 자료에 쓰고, `eval/reports/moderation.csv`는 대시보드가 읽어요.

**준비물**: 3-6이 main에 들어감, 파트 1의 겹침 확인 끝, 파트 1이 준 v4 모델 폴더(`model.onnx`, `tokenizer.json` 등. 커밋 금지, 레포 밖에 두기). 파트 1과 **먼저** 정할 것: ① "기존 필터"가 무엇인지 (아래) ② 3-4의 오탐률 계산법

**순서**

1. main으로 돌아오고, 모델이 정말 연결됐는지 확인해요. 폴더 이름이 `model_version`에 나와야 해요. 새 터미널이면 3-1의 `mod`를 다시 붙여 넣어요.

   ```sh
   git switch main && git pull
   uv sync --group ml --group onnx
   export MODERATION_ONNX_PATH=$HOME/aip-models/moderation-v4-onnx
   mod '존맛'
   ```

   경로는 파트 1이 준 폴더를 내가 둔 곳으로 바꿔요. `is_profane=False`, `model_version='moderation-v4-onnx'`가 나와야 해요. `dict`가 나오면 연결이 안 된 거예요.

2. A셋을 만들어요. 공개 데이터라 다시 받아야 해요 (인터넷 필요, gitignore).

   ```sh
   uv run python ml/moderation/prepare_public.py
   wc -l data/moderation/eval_only/*.jsonl
   ```

   `kmhas_test` 15035줄, `kotox_test` 1031줄, `unsmile` 8601줄이 나와요.

   A는 `kmhas_test.jsonl`이에요. `unsmile.jsonl`은 욕 없는 악플도 섞인 참고용이라 결과를 본문에 쓰지 않아요. A셋 라벨은 다시 검수하지 않아서 틀린 라벨이 섞여 있을 수 있어요.

3. B셋을 만들어요. B셋을 만드는 스크립트는 레포에 없어요. `kotox_test.jsonl`(공개 우회 문장)을 그대로 쓰고, 규칙별로 보고 싶으면 이렇게 한 번 만들어요. A셋 문장에 변형 규칙 10종을 걸어 1,000줄을 만들고, gitignore된 `eval_only/`에 저장해요.

   ```sh
   uv run python - <<'EOF'
   import json, random, sys
   sys.path[:0] = ["ml/moderation"]
   from pathlib import Path
   from augment import RULES
   from build_train import label_safe, read_jsonl
   rng = random.Random(2026)
   src = [r for _, r in read_jsonl(Path("data/moderation/eval_only/kmhas_test.jsonl"))]
   rng.shuffle(src)
   out = []
   for rule in RULES:
       for label in (0, 1):
           done = 0
           for r in src:
               if done == 50:
                   break
               new = rule(r["text"], rng)
               if r["label"] == label and new != r["text"] and label_safe(r["text"], new, label):
                   out.append({"text": new, "label": label, "type": rule.__name__})
                   done += 1
   Path("data/moderation/eval_only/b_auto.jsonl").write_text(
       "".join(json.dumps(r, ensure_ascii=False) + "\n" for r in out), encoding="utf-8")
   print(len(out), "줄")
   EOF
   ```

   여기서 나온 문장은 C, D가 아니에요. `b_auto.jsonl`은 커밋하지 않고, 어느 커밋에서 만들었는지(`git rev-parse --short HEAD`)를 적어요.

4. 아래를 위에서부터 돌려요. `--label`은 모델 이름이에요. **공식 점수는 새 팀의 기본값인 3(엄격)이고, 2(보통)도 같은 시험지로 재서 둘 다 적어요** (2026-11-25, 점수를 보기 전에 정함). 유리한 쪽만 고르지 않아요. A셋은 15,035줄이라 몇 분에서 수십 분 걸려요.

   ```sh
   run() { for t in moderation/test_C_human/test moderation/test_D_fp/test moderation/eval_only/kmhas_test moderation/eval_only/kotox_test moderation/eval_only/b_auto; do uv run python eval/moderation/score.py data/$t.jsonl --label "$1" --level "$2"; done; }
   MODERATION_ONNX_PATH= run dict-v1 3
   MODERATION_ONNX_PATH=$HOME/aip-models/moderation-v4-onnx run kcelectra-v4 3
   MODERATION_ONNX_PATH=$HOME/aip-models/moderation-v4-onnx run kcelectra-v4-level2 2
   ```

   차례로 "지금 사전만", "사전 + v4 (엄격)", "사전 + v4 (보통)"이에요. 모델이 없으면 단계가 달라도 결과가 같아요.

   점수는 `eval/reports/moderation.csv`에 쌓여요. 연습으로 한 번 더 돌렸으면 그 줄을 지우고 커밋해요 (같은 줄이 두 번 들어가요).

5. **"기존 필터"는 ③ korcen 1.0.3으로 정했어요** (파트 1, 2026-11-25, 사람 테스트셋 점수를 보기 전). +20%p는 `korcen-1.0.3`과 `kcelectra-v4`(3단계)의 C셋 F1 차이예요. 지금 사전만(`dict-v1`)의 점수도 **꼭 같이** 적어서 모델을 붙인 효과를 보여 줘요. ②는 하고 싶을 때만 재요.

   ```sh
   for s in C_human D_fp; do MODERATION_ONNX_PATH= uv run --with korcen==1.0.3 python eval/moderation/score.py data/moderation/test_$s/test.jsonl --label korcen-1.0.3 --system korcen; done
   ```

   korcen은 이 명령에서만 잠깐 받아 쓰고 프로젝트에는 설치하지 않아요. `SyntaxWarning`이 떠도 괜찮아요. korcen은 이전 메시지를 보지 않으니 `context`가 있는 문장이 몇 개인지도 적어요.

   * ① 지금 사전만: 4번의 `dict-v1`. 우리가 이미 고친 사전이라 "개선 전"이 아니에요
   * ② 개선 전 사전: 파트 3이 고치기 전 코드(`4b45d02`)를 따로 풀어서, 지금 채점기로 재요

     ```sh
     mkdir -p ../dict-v0 && git archive 4b45d02 | tar -x -C ../dict-v0
     cp eval/moderation/score.py ../dict-v0/eval/moderation/score.py
     cp data/moderation/test_C_human/test.jsonl ../dict-v0/data/moderation/test_C_human/
     cp data/moderation/test_D_fp/test.jsonl ../dict-v0/data/moderation/test_D_fp/
     (cd ../dict-v0 && for s in C_human D_fp; do MODERATION_ONNX_PATH= uv run --project ../server python eval/moderation/score.py data/moderation/test_$s/test.jsonl --label dict-v0 --report ../server/eval/reports/moderation.csv; done)
     rm -r ../dict-v0
     ```

     `../dict-v0`에는 테스트 문장이 들어 있으니 마지막 줄로 꼭 지워요. 샘플로 돌리면 C `f1` 0.5714, D `false_positive_rate` 0.25가 나와요. `cd`한 폴더 이름이 `server`가 아니면 `../server`를 내 폴더 이름으로 바꿔요.
   * ③ 오픈소스 필터 korcen: 위 명령. 다른 공개 모델을 더 재려면 파트 1과 정하고, 점수를 손으로 낼 때는 판정(0/1)을 시트 한 열에 받아서 F1을 계산해요: C셋의 TP는 `=SUMPRODUCT((M2:M="O")*(B2:B="C")*(L2:L&""="1")*(P2:P&""="1"))`, FN은 마지막을 `="0"`으로 바꿔 세요. C는 욕만 있어서 FP는 0이고, F1은 `2*TP/(2*TP+FP+FN)`이에요 (`L`은 label, `P`는 판정 열). 웹 사이트나 외부 API에 테스트 문장을 보내지 않아요
   * +20%p를 못 넘어도 기준을 바꾸지 않고 그대로 적어요

6. 유형별로 나눠 봐요.
   * C: `f1_by_type`을 봐요. C는 label 1만 있어서 F1이 잡아낸 비율만으로 정해져요. 무엇이든 욕이라고 하는 필터도 1.0이 나오니 **C는 항상 D 오탐률과 나란히** 적어요. 표본이 작으니 "10개 중 8개"처럼 개수도 적어요. CSV에는 유형별 점수가 없으니 화면에서 옮겨 적어요
   * D: `f1_by_type`을 **쓰지 않아요.** D는 욕이 없어서 한 개라도 잘못 잡으면 그 유형이 0.0, 하나도 안 잡으면 1.0으로만 나와요. 전체 `f1`도 0.0이나 1.0이에요. 오탐률은 유형마다 나눠서 따로 재요

     ```sh
     grep -F '"type": "약점: 해 이름"' data/moderation/test_D_fp/test.jsonl > /tmp/part3-d.jsonl
     uv run python eval/moderation/score.py /tmp/part3-d.jsonl --label kcelectra-v4 --report /tmp/part3-try.csv
     rm /tmp/part3-d.jsonl
     ```

   * 전체 D와 "약점 제외 D"는 같은 방법으로 `grep -vF '"type": "약점:' data/moderation/test_D_fp/test.jsonl > /tmp/part3-d.jsonl`로 뺀 것을 재요

7. `ms_per_msg`는 참고로만 봐요. 모델을 여는 시간이 들어가서 줄 수가 적은 C, D는 크게 나오고, 컴퓨터마다 달라요. 응답 시간 숫자(목표 100ms)는 파트 1이 재요. 발표에 이 칸을 옮겨 적지 않아요.

8. 마지막에 결과 파일을 커밋해요. 이때도 Claude Code를 꺼요.

   ```sh
   git switch -c eval/moderation-v4
   git add eval/reports/moderation.csv && git commit -m "Add moderation v4 scores"
   git push -u origin eval/moderation-v4
   ```

   PR에 적을 것: 서버 커밋(`git rev-parse --short HEAD`), 모델 폴더 이름, `level` 3과 2, 비교 대상과 이유, 유형별 표(개수), 약점 표, 목표를 못 넘은 것. "사람 테스트셋"이라는 것과 건수, A/B는 학습하지 않은 공개 데이터라는 것도 같이 적어요. 틀린 문장은 붙이지 않아요.

**확인할 것**
* 깨 보기: `MODERATION_ONNX_PATH=/없는폴더`로 C 샘플을 채점하면 오류 없이 `f1` 1.0이 나오고 경고(`moderation classifier failed: ...`)만 떠요. 그 줄이 보이면 **그 점수는 모델 점수가 아니에요.** 점수를 낼 때마다 이 경고가 없었는지 봐요
* 깨 보기: `--level 2`와 `--level 3`으로 D를 돌려 봐요(`--report /tmp/part3-try.csv`). 두 단계는 모델 점수 기준(0.7, 0.5)만 달라서 결과가 거의 같아요. 크게 다르면 파트 1에게 알려요
* 빈 파일을 채점하면 `n` 0에 `f1` 1.0이 나와요. 점수표의 `n`이 시트 건수와 같은지 봐요
* `eval/reports/moderation.csv`의 `testset` 칸이 `moderation/test_C_human/test`, `moderation/test_D_fp/test`로 같아야 대시보드에서 한 줄로 비교돼요

**끝 기준**: CSV에 C, D, A, B의 `dict-v1`, `kcelectra-v4`와 "기존 필터" 점수가 있고, PR에 유형별 표와 약점 표, 목표 달성/미달이 적혀 있어요. 목표를 못 넘은 것도 그대로 적어요.

**넘길 것**: `eval/reports/moderation.csv`와 표를 파트 1, 4에게. 대시보드는 web의 `public/reports/`에 이 CSV를 복사해야 진짜 점수로 바뀌니 파트 2에게도 알려요.

**막히면**
* `moderation classifier failed: ModuleNotFoundError`: onnxruntime이 없어요. `uv sync --group ml --group onnx`를 해요
* `moderation classifier failed: Exception`이나 `NoSuchFile`: 폴더 경로가 틀렸거나 그 안에 `model.onnx`, `tokenizer.json`이 없어요
* 점수가 이상하게 높거나 낮으면 `n`, 경고 줄, `testset` 이름, `MODERATION_ONNX_PATH`를 순서대로 봐요. 그래도 모르면 명령과 출력(문장이 나오는 "틀린 예시" 줄은 지우고)을 파트 1에게 보내요
