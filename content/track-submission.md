# 📝 플랫폼 제출 초안 (필수 필드)

> JUNCTION Platform 제출 폼의 필수(*) 필드 초안. 확정되면 이 페이지를 갱신해 최종본으로 유지합니다.
> ⚠️ **Project Name은 반드시 `[팀번호]` 로 시작** (킥오프 규정 — 미준수 시 페널티 가능).

## 1. 프로젝트 이름 후보 3종

솔루션 정체성: **"손에 쥘 수 있는 작은 모델들의 스쿼드가, 아껴 쓴 토큰으로 거대 모델급 답을 낸다."** 이와 얼라인된 2~3음절, 한/영 동시 직관 후보:

### 🥇 TOKKI (토끼) — 추천
- **토큰(TOKen) + 아끼다(KKI)** 의 말장난이 그대로 이름 — 채점 30점 항목인 토큰 효율이 정체성에 박혀 있음
- 토끼 = 작고 빠르고 꾀 많은 동물. "거북이(거대 모델)를 이기는 토끼" 서사를 뒤집어 쓸 수 있음
- 영어 화자도 "TOKKI"를 즉시 발음 가능, 한국 심사위원에겐 즉시 귀여움
- 시각화 마스코트 활용 가능: 에이전트 노드를 토끼로, give-up은 "깡총 물러남"으로 — 데모 엑스포에서 참가자 투표(60%)에 먹히는 비주얼

### 🥈 HANDFUL (핸드풀)
- 키노트의 출제 문장 *"a model you can actually **hold in your hands**"* 를 그대로 이름으로 응수 — Lablup 심사위원이 반드시 알아챔
- 영어 관용구 *"quite a handful"* = "작지만 만만치 않은 녀석" 이중 의미
- 2음절(hand-ful), 한글 표기도 직관적

### 🥉 AREUM (아름)
- "한 **아름**" = 두 팔로 안을 수 있는 분량 — "손에 쥘 수 있는 모델"의 가장 시적인 번역
- 아름답다의 "아름"과 동음 — 한국적 정서 + 국제 무대에서 이름으로 무리 없음
- 감성 피치로 갈 경우 최강, 단 말장난 임팩트는 TOKKI보다 약함

## 2. Project Name *

```
[팀번호] TOKKI
```

(예: `[63-1] TOKKI` — 팀번호는 팀 확인 후 기입)

## 3. Punchline * (200자 이내)

**영문 (권장 — 피치덱이 영문 필수라 통일):**

```
TOKKI is a squad of pocket-size models on Korean NPUs that routes, solves,
verifies — and knows when to quit. Big-model answers at a fraction of the tokens.
```

**국문 대안:**

```
토끼(TOKKI): 손에 쥐는 작은 모델들의 스쿼드. 풀고, 검증하고, 물러날 때를 알아서
거대 모델급 정답을 토큰 몇 분의 일로 냅니다.
```

## 4. Team Number *

```
(팀 등록 확인 후 기입 — Project Name의 [팀번호]와 반드시 일치시킬 것)
```

## 5. Description * (5000자 이내, 마크다운 지원)

```markdown
# TOKKI — the token-thrifty agent squad

## The Question We're Answering
Lablup × FuriosaAI asked: **"How far can you go with a model you can
actually hold in your hands?"** Frontier AI lives behind massive
infrastructure owned by the biggest tech companies. We believe the answer
to accessibility is not a bigger model — it's a smarter structure.

## Our Answer: Structure over Size
TOKKI is an AI agent squad built on **AI:GO's Squad functionality**,
running entirely on models served by **FuriosaAI RNGD** NPUs. Instead of
one giant brain, TOKKI organizes small models into specialized roles:

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
