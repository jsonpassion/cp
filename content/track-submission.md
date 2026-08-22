# 📝 플랫폼 제출 정보 (JUNCTION Platform 필수 필드)

🕒 **최신 반영: 2026-08-23 03:20 KST** · 스쿼드(.squad.json + one-shot) 제출 절차와 복사 명령은 [최종 제출 세트] 탭. 이름 후보 검토 기록(3라운드)과 구 제출 세트(v3.5 ~ v7.1)는 [📚 아카이브 › 제출 초안 전문].

> ⚠️ **Project Name은 반드시 `[팀번호]` 로 시작** (킥오프 규정 — 미준수 시 페널티 가능). 최종 마감 **8/23 12:00**, 지각 제출 불가 — 우리 자체 데드라인 **11:30**.

## 운영진 FAQ 확정 사항 (Trisha Bak, 8/22 00:09)

- 필수(*) 필드만 작성하면 됨 — 킥오프는 트랙·아이디어 확인용
- **Team Number는 대시 제외 3자리**: 우리 팀 **54-1 → `541`**
- Project Name은 팀명 그대로 써도 무방
- Submit Final 후 status **Final** 확인 (필요시 Draft로 되돌리기 가능)
- **제출 후에도 트랙 변경 가능**

## 제출 폼 입력값

| 필드 | 값 |
| --- | --- |
| Project Name * | `[541] BIBIMBAP` |
| Team Number * | `541` (팀번호 54-1, 대시 제외 3자리) |
| Challenge Picker * | Lablup / Build the Ultimate Agent Squad |
| Punchline * (200자) | 아래 |
| Description * (5000자, 마크다운) | 아래 초안 — 제출 전 최종 스쿼드 기준으로 갱신 |
| Project Demo | https://jsonpassion.github.io/bibimbap/ (`viewer/viewer.html` · `viewer/kids.html`) |
| Source Code | https://github.com/jsonpassion/bibimbap (public, MIT, 오픈소스 명시) |
| Presentation | `bibimbap/deck/deck.pdf` (12p, 영문) — Google Drive **"링크가 있는 모든 사용자"** 공개 링크 (시크릿 창에서 열어 확인) |
| Project Summary | `bibimbap/deck/summary.md` (1,196자) |
| Video | (여유 시) 뷰어 리플레이 30초 녹화 |

제출 후: status **final** 확인 → Discord `✅-confirm-submission` 채널에서 반영 확인(스크린샷) → 별도 제공 폼에도 제출(필수 아님, 강력 권장).

## Punchline * — ✅ 확정

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

> ⚠️ 문구 점검: "instruct and reasoning agents mixed"는 킥오프 시점 표현. 최종 스쿼드는 gpt-oss-120b 단일(2 에이전트)이며 "섞어 보고 이기는 재료만 남겼다"가 실제 이야기 — 제출 시 유지/수정 여부만 결정.

> **선정 이유 (직관성 기준)**: 전 세계가 아는 단어라 국제 심사위원에게 1초 전달, 한국 참가자(투표 60%)에겐 웃음과 공감. 이름이 곧 아키텍처 — 서로 다른 재료를 한 그릇에 비벼 완성하는 스쿼드. 한국 NPU 위에 한국 음식 이름 = 소버린 AI 서사까지 자동 완성.

## Description * — 초안 (킥오프 제출본)

> ⚠️ 아래 "Our Answer" 문단의 Router / Solver(s) / Verifier / Give-up Judge 구조는 **킥오프 시점 설계**. 최종 스쿼드는 **에이전트 2(Conductor 플래너가 직접 답 + Solver), 전원 gpt-oss-120b, DIRECT**이며 서사는 "관측 → 절감 → 재투자 실험 → 단순화". 제출 전에 `bibimbap/deck/summary.md`(1,196자) 기준으로 이 문단을 갱신할 것. 나머지(질문·Observability·Tech Stack·Why It Matters)는 그대로 유효.

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

## 🎤 발표 산출물 — `bibimbap/deck/` (리포에 푸시됨)

| 파일 | 내용 |
| --- | --- |
| `deck.html` / **`deck.pdf` (12p)** | 1 타이틀 · 2 키노트 질문/답 · 3 만든 것 · 4 43× · 5 Trace가 보여준 3가지 · 6 ÷17 · 7 측정 문화(전수 표) · 8 리더보드 교훈(누구의 마지막 말이 채점되는가) · 9 최종 DIRECT(+ 앙상블 실험 0.045) · 10 시각화 6축 · 11 토큰 곡선 · 12 클로징 |
| `summary.md` | 제출 폼 요약 1,196자 |
| `speaker-notes.md` | 4분 대본 · 장표별 타이밍 · Q&A 숫자 출처표 |
| 편집 필요 | 9장 `final-score-value`(리더보드 최종 점수) · 12장 `team-members`(팀원 이름 — 소스에 없어 비워 둠) |

## 🌐 공개 리포 · 데모 URL

- **리포**: https://github.com/jsonpassion/bibimbap (public, MIT) — `viewer/viewer.html`(Trace Viewer v3.3) · `viewer/kids.html`(의인화 그림책) · traces(공개 연습 세트 로컬 완주 로그만) · `normalize.py` · `analyze_run.py` · `test.js` · docs
- **데모(GitHub Pages)**: https://jsonpassion.github.io/bibimbap/ — 플랫폼 제출 폼의 **Project Demo URL**
- 비밀키·히든 문항 데이터 없음(푸시 전 스캔 완료). 리포 README에 오픈소스 라이브러리·practice set attribution 명시, 히든 세트 구성 서술 금지.
