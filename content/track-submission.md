# 📝 플랫폼 제출 초안 (필수 필드)

> JUNCTION Platform 제출 폼의 필수(*) 필드 초안. 확정되면 이 페이지를 갱신해 최종본으로 유지합니다.
> ⚠️ **Project Name은 반드시 `[팀번호]` 로 시작** (킥오프 규정 — 미준수 시 페널티 가능).

## 운영진 FAQ 확정 사항 (Trisha Bak, 8/22 00:09)

- 필수(*) 필드만 작성하면 됨 — 킥오프는 트랙·아이디어 확인용
- **Team Number는 대시 제외 3자리**: 우리 팀 **54-1 → `541`**
- Project Name은 팀명 그대로 써도 무방
- Submit Final 후 status **Final** 확인 (필요시 Draft로 되돌리기 가능)
- **제출 후에도 트랙 변경 가능**

## 📦 `.squad.json` 추출 → 제출 폼 붙여넣기 절차 (제출 사이트 문언 기준, 2026-08-22 17:35)

**제출 폼이 요구하는 파일은 단 하나** — AI:GO가 **스쿼드 워크스페이스 루트**에 쓰는 `.squad.json`. 폼 문언: *"AI:GO writes this file at the root of your squad's workspace directory … and rewrites it every time you save the squad. Paste it whole and unedited."* 그리고 *"A Squad Template JSON is not a submission"* — 앱의 **Export(템플릿 내보내기) 파일이 아니라 워크스페이스 파일**이어야 한다.

```mermaid
flowchart LR
    A["AI:GO GUI에서<br/>프롬프트 수정"] --> B["Save (저장)<br/>→ .squad.json 재작성"]
    B --> C["check_squad.py<br/>버전·플래너·크기 판독"]
    C -->|"❌ 구버전"| A
    C -->|"✅ v3.4"| D["--copy → 클립보드"]
    D --> E["제출 폼: Your squad's .squad.json<br/>통째로 붙여넣기 (수정 금지)"]
    E --> F["one-shot 3종 붙여넣기<br/>(웹앱 B 섹션 복사 버튼)"]
    F --> G["Check without submitting<br/>(무료·무제한)"]
    G -->|"No problems found"| H["Submit to the queue"]
```

### 단계

| # | 무엇을 | 어떻게 | 확인 |
| --- | --- | --- | --- |
| 1 | GUI에서 카드 편집 후 **반드시 Save** | 저장 전엔 파일이 구버전 그대로 (8/22 15:55 사고 재발 방지) | 파일 저장 시각이 방금인지 |
| 2 | 파일 판독 | `python3 ~/Documents/Developer/jxc-selfeval/check_squad.py` | 5개 전부 **v3.4 ✅**, 플래너 Conductor, 크기 ≪ 1 MiB |
| 3 | 복사 | `python3 ~/Documents/Developer/jxc-selfeval/check_squad.py --copy` (pbcopy) — 숨김 파일이라 Finder에선 ⌘⇧. 로 표시 | "📋 클립보드에 복사됨" |
| 4 | 폼에 붙여넣기 | **Your squad's .squad.json** 칸에 통째로. 한 글자도 수정 금지 | — |
| 5 | one-shot 3종 | coding/math/generic 각 칸에 웹앱 프롬프트 탭 B 섹션 복사 버튼 → `{{TASK}}` 리터럴 포함, ≤32 KB | 3칸 모두 |
| 6 | **Check without submitting** | 러너와 동일 규칙으로 검증, 큐 점유 0·쿨다운 0·횟수 무제한 | "No problems found" (경고 24건은 정상: settingsOverrides 미전달·도구 무효) |
| 7 | **Submit to the queue** | 대기 1 · 실행 1 (팀당) — 앞선 런이 끝나야 다음이 돈다 | 폼 상단 "Waiting 1 of 1" |

### 파일 안에 들어가는 것 / 평가에서 무시되는 것

- **전달됨**: 에이전트 이름 · role(`{type: planner}` / custom) · **systemPrompt** · description · modelPreferences(preferredModelId) · memoryEnabled
- **무시됨**(Check 경고로 확정): `settingsOverrides`(maxTokens·maxToolCalls) · `toolConfig.enabledTools`(평가는 tool-less) → GUI 저장 시 도구가 다시 켜져도 무방
- 구조: `{squadId, squadName, initializedAt, appVersion, config: {agents: [...]}}` — 에이전트 배열은 `config.agents`

### 🏁 최종(4차) 제출 세트 — **v3.8** (08-22 20:37 준비 완료, GUI 검증 후 제출)

구성: **Conductor v3.8**(Qwen3-32B `/no_think`, 685 B) + **Generic-Solver v3.7b** + **Math·LCB·SWE v3.6** + **one-shot v2.3**(279 B, directive + "Solve this problem:"). 3차 대비 플래너 입력 −1,249 tok/문항, 솔버 동일.

**① 에이전트 카드 — 복사 명령 (각 카드 편집창에 ⌘V → 전부 끝나면 Save)**

| 카드 | 시스템 프롬프트 | 설명(description) |
| --- | --- | --- |
| Conductor (플래너 · Qwen3-32B-FP8) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_conductor_v3.8.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_conductor.txt` |
| Generic-Solver (gpt-oss) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_generic_v3.7b.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_generic.txt` |
| Math-Solver (gpt-oss) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_math_v3.6.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_math.txt` |
| LCB-Coder (gpt-oss) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_lcb_v3.6.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_lcb.txt` |
| SWE-Patcher (gpt-oss) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_swe_v3.6.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_swe.txt` |

(솔버 4장은 19:19 저장본과 동일하므로 **Conductor만 교체**해도 됨. 설명 필드는 선택.)

**② GUI 검증 (Save 후 1문항씩 3회 → `analyze_run.py` 태스크 1·담당 정확)**: `pbcopy < ~/Documents/Developer/jxc-selfeval/gui_math_lean.txt` · `gui_generic_lean.txt` · `gui_coding_lean.txt`

**③ 제출 폼 4칸 — 복사 명령**

| 칸 | 명령 |
| --- | --- |
| Your squad's .squad.json | `python3 ~/Documents/Developer/jxc-selfeval/check_squad.py --copy` (판정 ✅ 확인 후 붙여넣기) |
| One-shot — coding | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/oneshot_coding_v2.3.txt` |
| One-shot — math | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/oneshot_math_v2.3.txt` |
| One-shot — generic | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/oneshot_generic_v2.3.txt` |

→ Check without submitting ("No problems found") → Submit to the queue (3차가 실행으로 넘어가 대기 슬롯이 비면).

### 3차 제출 세트 — v3.7b (18:44 이후 큐 대기)
 (19:35 제출 진행)

| 파일 (`jxc-selfeval/submissions/`) | 내용 | 폼 칸 |
| --- | --- | --- |
| `3rd_v3.7b.squad.json` | 19:19 저장본 · 17,432 bytes · Conductor **Qwen3-32B `/no_think`**(프롬프트 v3.4) + Generic-Solver **v3.7b**(private reasoning, 140문항 80.9%) + Math·LCB·SWE **v3.6**(답만 출력) · 전원 도구 무효·메모리 OFF | Your squad's .squad.json |
| `oneshot_coding_v2.2.txt` (1,251 B) · `oneshot_math_v2.2.txt` (1,142 B) · `oneshot_generic_v2.2.txt` (1,044 B) | [PLANNING DIRECTIVE] + [FINAL RESPONSE RULE] + [SOLVING INSTRUCTIONS] + "Solve this problem:" + `{{TASK}}` | One-shot 3칸 |

검증 근거: 3트랙 GUI 완주 태스크 1 (math·generic·coding, `/no_think` 후 재확인) · 플래너 2턴 61 tok · 솔버 부트스트랩 generic 80.9% / math 100% / LCB 90% · Check 경고 15건(평가 미전달 계열만).

### 제출 이력

| 차수 | 시각 | 스쿼드 | one-shot | 비고 |
| --- | --- | --- | --- | --- |
| 1차 | 8/22 14시 | v2 (8 agents, gpt-oss 플래너, 중복 분해) | v2 | 판독 대기 — 채점 경로·가중치 확인용 |
| 2차 | 18:44 | v3.5 (5 agents, Qwen3 플래너 thinking, Generic에 Confidence 줄 잔존) | v2.1 | 큐 배치 처리 대기 |
| **3차 (최종)** | **19:35** | **v3.7b** | **v2.2** | 위 세트 |

### (이전) 2차 제출 완료 (18:44, v3.5)

- 2차: `.squad.json` 17:52본(Qwen3 플래너) + one-shot v2.1 → 큐 진입. 백업 `jxc-selfeval/submissions/2nd_v3.5_1844.squad.json`
- 3차(최종): 위 v3.7b 세트로 제출 진행

### 현재 파일 상태 (check_squad.py, 8/22 17:52 저장본 = **v3.5**) — ✅ 제출 가능

| 항목 | 상태 |
| --- | --- |
| 프롬프트 | **5/5 v3.4** (Agent economy · only-agent RULES) |
| 플래너 모델 | **Conductor = Qwen3-32B-FP8** (A/B 4/4 태스크 1) · 솔버 4종 gpt-oss |
| 모델 | 5/5 gpt-oss-120b · 플래너 Conductor · 16.8 KB |
| Check 결과 | **No problems found · 경고 15건** = settingsOverrides 5 + tools/enabledTools 5×2 — 전부 "평가에 미전달" 계열, 정정 불필요 |
| 남은 선택 사항 | ① 설명(description) 5장 공란 — 플래너 로스터 표시용 1줄씩 입력 권장(directive가 담당을 지정하므로 필수는 아님) ② 도구 토글 OFF는 경고만 줄일 뿐 평가 무관 — 시간 남으면 |
| 확인된 사실 | Conductor의 enabledTools 6개(read_file·write_file·list_files·list_directory·search_files·get_current_time)에 **create_task 없음** → 태스크 생성은 내부 플래너 기능이며 tool-less 경고 대상이 아님 |

## 1. 이름 후보 아카이브 (검토 기록)

> ✅ **최종 확정: `[541] BIBIMBAP`** — 아래는 검토 과정 기록입니다.

공통 기준이 된 솔루션 정체성: **"손에 쥘 수 있는 작은 모델들의 스쿼드가, 아껴 쓴 토큰으로 거대 모델급 답을 낸다."**

### 1라운드 — 2~3음절, 한/영 동시 직관

#### TOKKI (토끼)
- **토큰(TOKen) + 아끼다(KKI)** 의 말장난이 그대로 이름 — 채점 30점 항목인 토큰 효율이 정체성에 박혀 있음
- 토끼 = 작고 빠르고 꾀 많은 동물. "거북이(거대 모델)를 이기는 토끼" 서사를 뒤집어 쓸 수 있음
- 영어 화자도 "TOKKI"를 즉시 발음 가능, 한국 심사위원에겐 즉시 귀여움
- 시각화 마스코트 활용 가능: 에이전트 노드를 토끼로, give-up은 "깡총 물러남"으로 — 데모 엑스포에서 참가자 투표(60%)에 먹히는 비주얼

#### HANDFUL (핸드풀)
- 키노트의 출제 문장 *"a model you can actually **hold in your hands**"* 를 그대로 이름으로 응수 — Lablup 심사위원이 반드시 알아챔
- 영어 관용구 *"quite a handful"* = "작지만 만만치 않은 녀석" 이중 의미
- 2음절(hand-ful), 한글 표기도 직관적

#### AREUM (아름)
- "한 **아름**" = 두 팔로 안을 수 있는 분량 — "손에 쥘 수 있는 모델"의 가장 시적인 번역
- 아름답다의 "아름"과 동음 — 한국적 정서 + 국제 무대에서 이름으로 무리 없음
- 감성 피치로 갈 경우 최강, 단 말장난 임팩트는 TOKKI보다 약함

### 2라운드 — 추가 후보 10종

| # | 이름 | 음절 | 뜻·유래 | 얼라인 포인트 |
| --- | --- | --- | --- | --- |
| 1 | **HANJUM (한줌)** | 2 | 한 줌 = a handful | 출제 문장 "hold in your hands"의 한국어 정답. 한 줌의 모델로 거대한 답 |
| 2 | **GONGGI (공기)** | 2 | 공기놀이 + 공기(air) | 손안의 작은 돌 여러 개를 다루는 기술 게임 = 스쿼드 조작. 공기처럼 흐르는 지능(전기 비유). 오징어게임으로 해외 인지도 有 |
| 3 | **BANDI (반디)** | 2 | 반딧불이 | 작은 불빛들이 모여 빛남 = 저전력 NPU + 스쿼드. Furiosa의 전력 효율 서사와 직결 |
| 4 | **DANDI (단디)** | 2 | 경상도 사투리 "단디 해라"(야무지게 해라) | 개최지 경북 로컬 감성(경상북도가 스폰서) + Verifier의 꼼꼼함. 영어로는 Dandy로 읽힘 |
| 5 | **DURE (두레)** | 2 | 전통 공동 노동 조직 | 한국식 '스쿼드'의 원형. 함께 일해 큰 일을 해내는 구조 그 자체 |
| 6 | **PODO (포도)** | 2 | 포도 = 작은 알갱이의 송이 | 작은 모델들이 한 송이(클러스터)로 — GPU cluster 말장난. 시각화 노드를 포도알로 |
| 7 | **KKOMA (꼬마)** | 2 | 꼬마 = 작지만 야무진 아이 | "작은 모델" 정체성을 가장 직설적으로. 귀여움 = 데모 어필 |
| 8 | **DOTORI (도토리)** | 3 | 도토리 → 참나무 | 손안의 작은 씨앗이 큰 지능이 된다. 국제적으로 acorn 스토리 즉시 전달 |
| 9 | **MODU (모두)** | 2 | 모두 = everyone | "Make AI Accessible **for 모두**" — Lablup 미션과 직결. AI for MODU라는 태그라인 가능 |
| 10 | **POCKET (포켓)** | 2 | pocket-size | 주머니 속 스쿼드. 한/영 모두 즉시 이해, 가장 안전한 선택 |

**추천 우선순위**: ① HANJUM (출제 문장의 한국어 화답 + 겸손하면서 강한 이미지) ② GONGGI (이중 의미 + 데모 스토리텔링) ③ BANDI (Furiosa 전력 서사 직결). 말장난 임팩트는 TOKKI, 문화 서사는 DURE/DANDI.

### 3라운드 — 음절 제한 해제, 재미·직관 우선 10종

| # | 이름 | 뜻·유래 | 얼라인 포인트 |
| --- | --- | --- | --- |
| 1 | **알잘딱 (ALJALTTAK)** | "알아서 잘 딱" (알잘딱깔센) | Router가 알아서, Solver가 잘, Give-up이 딱 끊는다 — **스쿼드 파이프라인이 이름 그 자체.** 한국 심사장 웃음 보장 |
| 2 | **국밥 (GUKBAP)** | 가성비·든든함의 밈 아이콘 | "성능 국밥" — 싸고 든든하게 정답 말아주는 AI. **토큰 효율 30점의 밈화** |
| 3 | **도깨비 (DOKKAEBI)** | "금 나와라 뚝딱" | 작은 방망이 하나로 뭐든 만들어냄 = 작은 모델로 거대한 답. K-드라마로 국제 인지도 |
| 4 | **비빔밥 (BIBIMBAP)** | 여러 재료를 한 그릇에 | **서로 다른 모델(instruct+reasoning)을 섞어 완성** — 이기종 조합 그 자체. 전 세계가 아는 단어 |
| 5 | **개미 (GAEMI)** | 개미군단 | 작은 일꾼들의 집단 지성 + 효율의 상징. "한 마리는 약하지만 군단은 강하다" |
| 6 | **티끌 (TIKKEUL)** | 티끌 모아 태산 | 작은 모델 모아 큰 지능 — 속담 하나로 컨셉 설명 끝 |
| 7 | **소수정예 (SOSU JEONGYE)** | 少數精銳 | "거대 군단 대신 소수정예" — 국문 전달력 최강, 피치 첫 문장 가능 |
| 8 | **깜냥 (KKAMNYANG)** | 제 깜냥을 안다 | **Give-up Judge의 철학이 이름**: 자기 한계를 알고 물러날 때를 아는 AI |
| 9 | **다윗 (DAVID)** | 다윗 vs 골리앗 | 작은 자가 거인을 이긴다 — 국제 무대 1초 전달. 영문 표기 그대로 DAVID |
| 10 | **알뜰 (ALTTEUL)** | 알뜰폰의 그 알뜰 | "알뜰 AI 요금제" — 같은 정답을 몇 분의 일 토큰으로. 알뜰폰 서사 차용 |

## 2. Project Name * — ✅ 확정

```
[541] BIBIMBAP
```

> **선정 이유 (직관성 기준)**: 전 세계가 아는 단어라 국제 심사위원에게 1초 전달, 한국 참가자(투표 60%)에겐 웃음과 공감. 그리고 이름이 곧 아키텍처 — **서로 다른 재료(instruct + reasoning 모델)를 한 그릇에 비벼 완성**하는 이기종 스쿼드. 한국 NPU 위에 한국 음식 이름 = 소버린 AI 서사까지 자동 완성.

## 3. Punchline * (200자 이내) — ✅ 확정

**영문:**

```
BIBIMBAP is a bowl of small models on Korean NPUs — instruct and reasoning
agents mixed to perfection, serving big-model answers at a fraction of the tokens.
```

**국문 대안:**

```
비빔밥(BIBIMBAP): 한국산 NPU 위에 작은 모델들을 한 그릇에 비볐습니다.
거대 모델급 정답을 토큰 몇 분의 일로 말아 드립니다.
```

## 4. Team Number *

```
541
```

(팀번호 54-1, 대시 제외 3자리 입력 — 운영진 FAQ 공식 안내)

## 5. Description * (5000자 이내, 마크다운 지원)

```markdown
# BIBIMBAP — small models, mixed to perfection

## The Question We're Answering
Lablup × FuriosaAI asked: **"How far can you go with a model you can
actually hold in your hands?"** Frontier AI lives behind massive
infrastructure owned by the biggest tech companies. We believe the answer
to accessibility is not a bigger model — it's a smarter structure.

Bibimbap is a Korean dish where separate ingredients — none of them a
meal on its own — are mixed in one bowl into something complete. That is
exactly our architecture.

## Our Answer: Structure over Size
BIBIMBAP is an AI agent squad built on **AI:GO's Squad functionality**,
running entirely on models served by **FuriosaAI RNGD** NPUs. Instead of
one giant brain, we mix small models into specialized roles:

- **Router** — triages each problem by type and difficulty. Easy items
  get a single-shot answer; hard ones wake the full squad.
- **Solver(s)** — per-track model assignment: instruct models where
  breadth wins, reasoning models only where deep thinking pays off.
- **Verifier** — re-solves the problem through an independent path and
  checks answer/format consistency (pure LLM verification — the squad
  uses no external tools at evaluation time).
- **Give-up Judge** — decides when a problem is no longer worth its
  tokens, submits the best attempt so far, and reallocates the saved
  budget to problems that can still be won.

Every prompt is structured with a **long stable prefix** (roles, rules,
exemplars) and a short variable tail (the problem), maximizing prefix-
cache hits and cutting effective token cost.

## Radical Observability
Every run emits a structured trace log. A standalone replay viewer
renders it as a live agent graph with a timeline scrubber, per-decision
rationale ("why did the Router escalate?", "why did we give up here?"),
and cross-run analytics — including how many tokens the Give-up Judge
saved. Observability, interpretability, traceability, explainability,
clarity, and insight are treated as product features, not afterthoughts.

## Tech Stack
- **AI:GO** (Lablup) — agent squad core path
- **FuriosaAI RNGD**-served model endpoints via Backend.AI
- Vanilla web (HTML/JS) trace replay viewer — runs offline from a log file
- Open-source libraries are listed and attributed in the repository

## Why It Matters
If a handful of pocket-size models on sovereign, power-efficient NPUs
can match big-model answers on a token budget, then intelligence really
can flow like electricity — reaching wherever it's needed. That is
"Make AI Accessible," demonstrated.
```

## 6. Challenge Picker *

```
Lablup 트랙 선택 (드롭다운에서 Lablup / Build the Ultimate Agent Squad 항목)
```

---

### 선택 필드 계획 (참고)

| 필드 | 계획 |
| --- | --- |
| Project Demo | 시각화 뷰어 배포 URL (P3 완성 후) |
| Source Code | 팀 리포 public 전환 후 URL |
| Video | 데모 30초 리플레이 녹화 (여유 시) |
| Presentation | 피치덱 PDF (8/23 오전 업로드) |
