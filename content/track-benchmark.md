# 🔬 벤치마크 구성 & 모델 (실측)

> 출처: 제출 시스템의 **공개 엔드포인트** (`/practice-sets/*/manifest.json`, `/api/leaderboard`, `/openapi.json`). 로그인 없이 조회 가능한 참가자용 공개 정보이며, 2026-08-22 확인 기준입니다.

## ✅ 공정성 점검: 이 데이터는 주최측 실수로 노출된 것이 아님

"문제 셋이 보이는 게 실수 아닌가"를 점검한 결과, **의도된 공식 공개**로 판정합니다. 근거:

1. `/practice-sets` 페이지의 공식 안내문이 명시: *"The visible benchmark sets, **published for teams to iterate against**"* — 팀들이 반복 실험하도록 공개한 연습 세트이며, *"they **never overlap the hidden sets** your ranked score is judged on"* — 실채점 히든 세트와 절대 겹치지 않음.
2. 키노트 슬라이드가 제출 사이트 URL을 전 참가자에게 공개했고, 리더보드도 "공개 표시"가 공식 방침.
3. SHA256SUMS·NOTICE.txt(라이선스 고지)·매니페스트·`visibility: visible` 라벨 등 **배포용으로 설계된 산출물**이 갖춰져 있음.
4. 인증이 필요한 영역(dev key, 사용량, run 상세)은 실제로 잠겨 있으며, 이 문서는 그 경계를 넘지 않은 정보만 담음.

**따라서 이 데이터를 활용하는 것은 정당하며 오히려 출제 의도입니다.** 단, 최종 산출물(피치덱·제출 리포) 작성 시 아래 가이드를 지킬 것:

- 데이터 출처는 "주최측이 공개한 practice set"으로 표기하고, 매니페스트의 attribution(하단 참조)을 리포에 포함할 것 (Code of Conduct의 출처 명시 의무).
- **히든 세트 구성(문항 수 분포 등)을 안다는 식의 서술은 피치덱에 넣지 말 것.** 공개 정보이긴 하나, 전략 노출이며 불필요한 오해(스크래핑 의혹)를 살 수 있음. "공개 연습 세트로 검증했다"까지만.
- 연습 세트 문항·정답 자체를 최종 결과물이나 데모에 재배포하지 말 것 (AIME는 라이선스 미선언 자료).

## 🚨 신규 발견: 평가 중 스쿼드는 도구를 쓸 수 없다

practice-sets 공식 안내문에 다음 문장이 있습니다:

> *"**your squad has no tools during a run** and never browses a repository"* — 평가 실행 중 스쿼드는 도구가 없으며, 저장소를 탐색하지 않는다. SWE-bench의 코드 맥락은 채점기가 직접 요청에 포함시켜 전달한다(context bundle이 그 사본).

이는 전략에 중대한 영향을 줍니다: **"Verifier가 sympy/테스트를 실행해 검증"하는 설계는 평가 시점에는 불가능**합니다. 코드 실행 검증은 개발 단계(프롬프트 튜닝, 자체 채점 루프)에서만 쓰고, 제출되는 스쿼드는 **순수 LLM 오케스트레이션(역할·프롬프트·라우팅)만으로** 검증 능력을 내야 합니다. 자세한 보정은 [필승 전략] 탭 참조.

또한 **published requests** 페이지가 있어, visible 문항마다 **채점기가 실제로 보내는 요청 전문**(렌더링된 문항 + REQUIRED OUTPUT 블록)을 볼 수 있습니다. 빈칸은 우리의 one-shot prompt 하나뿐 — **프롬프트 설계는 반드시 이 실제 요청 포맷 기준으로** 할 것.

## ⚠️ 가장 중요한 발견 — 벤치마크는 3트랙이다

미션 설명의 "수학·코딩 등"이 막연한 표현이 아니었습니다. 채점은 **정확히 3개 트랙**으로 나뉩니다.

```
coding · math · generic
```

그리고 **히든 세트의 문항 수 분포가 전략을 완전히 뒤집습니다.**

```mermaid
pie showData
    title 히든(실채점) 세트 문항 수 상한
    "generic (MMLU-Pro)" : 698
    "coding (SWE-bench + LiveCodeBench)" : 240
    "math (AIME + MATH-500)" : 66
```

| 트랙 | 히든 문항 수 | 공개(연습) 문항 수 | 구성 |
| --- | --- | --- | --- |
| **generic** | **448 ~ 698** | 140 | MMLU-Pro (카테고리당 20문항) |
| **coding** | 140 ~ 240 | 60 | SWE-bench-lite 150, LiveCodeBench-v6 40 |
| **math** | 60 ~ 66 | 164 | AIME 2024 30, MATH-500 level-5 134 |

> 🔥 **수학은 전체의 10% 미만입니다.** 대부분의 팀이 "고난도 수학 문제 풀이"에 스쿼드를 최적화하겠지만, 실제 점수의 대부분은 **MMLU-Pro 객관식 지식 문제**에서 나옵니다. 여기가 이번 트랙의 최대 정보 우위입니다.

## 채점 방식 (트랙별)

| 트랙 | 채점기 | 상세 |
| --- | --- | --- |
| math | `math_answer` | 정수형 답 → `integer_exact` + `math_verify` / 수식형 답 → `math_verify` |
| coding | `livecodebench_tests`, `swebench_docker` | **실제 테스트 실행**으로 채점 (Docker) |
| generic | (MMLU-Pro 객관식) | 정답 선택지 대조 |

- **math는 `run_repeats: 2`** — 문제를 2회 반복 실행해 **풀린 문제 전체에 대한 평균 정확도**로 집계합니다. 즉 운으로 한 번 맞히는 것이 통하지 않고, **재현성 있는 정답**이 필요합니다. 동시에 math는 토큰을 2배로 먹습니다.
- 공개 연습 세트는 `/practice-sets/`에서 직접 내려받을 수 있습니다 (`items.jsonl`, `visible-sets.zip`, `SHA256SUMS` 제공). **자체 채점 루프를 여기에 붙이면 제출 없이 무료로 성능을 측정**할 수 있습니다.

## 🚨 실행 캡 (Caps) — 공식 경고

리더보드 API가 반환하는 공지에 다음 내용이 명시되어 있습니다.

> 이 페이지의 모든 실행은 **항목별 wall-clock 상한**과 **실행별 토큰 상한** 아래에서 수행되었으며, 이는 모든 팀에게 동일합니다. 서로 다른 상한에서 실행된 결과는 직접 비교할 수 없고, 그 차이는 작은 보정 수준이 아닙니다. **평가 모델 3개 중 2개는 reasoning 모델**이며, 그중 하나는 **답을 내놓기 전 출력 예산의 97%를 reasoning 토큰에 소비**하는 것으로 측정되었습니다. 따라서 토큰 상한은 **reasoning 모델로 구성한 스쿼드에 instruct 모델 기반 스쿼드보다 대략 한 자릿수(약 10배) 더 가혹하게 작용**합니다.

API 스키마상 캡은 세 가지입니다: `per_run_token_cap`, `per_item_wallclock_seconds`, `infra_retries`.

### 이것이 의미하는 것

```mermaid
flowchart TD
    R["reasoning 모델로<br/>전 트랙 처리"] --> R1["출력 예산의 97%가<br/>사고 토큰으로 소모"]
    R1 --> R2["토큰 상한 조기 도달<br/>= capped_tokens"]
    R2 --> R3["❌ 남은 문항 미응답<br/>정확도 폭락 + 효율 점수 폭락"]
    I["트랙별 모델 분리"] --> I1["generic·쉬운 코딩<br/>= instruct 모델"]
    I1 --> I2["아낀 예산을<br/>math·SWE-bench에 집중"]
    I2 --> I3["✅ 40점·30점 동시 확보"]
```

**평가 모델 구성: instruct 1개 + reasoning 2개.** 키노트 대시보드 화면에서 확인된 instruct 계열 모델은 **`Qwen3-30B-A3B-Instruct-2507-FP8`** 입니다. 나머지 2개 reasoning 모델의 정확한 ID는 팀 로그인 후 **Development keys / Development usage** 페이지 또는 dev key로 `/v1/models`를 호출하면 확인됩니다.

## ✅ 여기서 도출되는 모델 배정 전략

| 트랙 | 권장 모델 | 이유 |
| --- | --- | --- |
| **generic (MMLU-Pro)** | **instruct 모델** | 객관식 지식 문제. 장문 사고가 정확도를 거의 못 올리면서 토큰 상한만 태움. **문항 수가 가장 많아 여기서 캡에 걸리면 치명적** |
| **coding (LiveCodeBench)** | instruct + 테스트 실행 검증 | 실행 채점이므로 사고보다 **실제 테스트 통과**가 중요 |
| **coding (SWE-bench)** | reasoning (선별 투입) | 저장소 맥락 파악이 필요한 난이도 |
| **math (AIME/MATH-500 L5)** | **reasoning 모델** | 여기서만 사고 토큰이 값어치를 함. 단 `run_repeats: 2`로 비용 2배 |

> 💡 **핵심 통찰**: "어떤 모델이 더 똑똑한가"가 아니라 **"어느 트랙에 사고 예산을 쓸 것인가"** 가 이번 트랙의 진짜 문제입니다. 문항 수가 가장 많은 generic에 reasoning 모델을 붙이는 순간 토큰 상한에 걸려 무너집니다.

## 데이터셋 출처 표기

제출물에 아래 출처를 명시하는 것이 안전합니다 (매니페스트가 제공하는 attribution).

- **SWE-bench** — Jimenez et al., ICLR 2024
- **LiveCodeBench** — Jain et al., 2024 (LeetCode / AtCoder / CodeForces 문제 기반)
- **MATH-500** — Hendrycks et al., level-5 subset (MIT)
- **AIME 2024** — Mathematical Association of America 자료
- **MMLU-Pro** — TIGER-Lab (MIT)

## 확인 방법 (직접 재현)

```bash
curl -sk https://submission.jxc.events.lablup.ai:8444/practice-sets/set.manifest.json
```

```bash
curl -sk https://submission.jxc.events.lablup.ai:8444/api/leaderboard
```
