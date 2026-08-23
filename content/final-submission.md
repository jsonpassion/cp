> 🟢 **08/23 09:50 — 9차(최종) 제출 완료·큐 진입: Conductor v6.6 + one-shot coding v3.7 / math v3.3 / generic v3.3.** 직전 8차(v6.4 + v3.5) = **0.363 (6위)**: coding 21.1 · math 46.2(채점 7/13) · generic 56.8. 최고는 7차 **0.373 (4위)**. 9차의 근거 두 가지 — ① **코딩 21.1% = 8/38 = LCB만 정답, SWE ~30문항 전멸(1위도 동일)**이며 원인은 풀이가 아니라 **패치 형식**(hunk 헤더가 숫자 없는 `@@` → git apply/patch 거부, 연습 SWE 5/5). 처방 = SWE 패치 계약(경로 정확, 3+3 컨텍스트, `@@ -S,L +S,M @@` L=6+삭제·M=6+추가): 숫자 헤더 0/8→8/8, LCB 회귀 20/20. ② **math 미채점 = 러너 토큰 cap에 잘린 추론**(AIME-2024 Reasoning: high 출력 중앙값 3,251·p90 6,453 tok; 4K cap이면 ~25% 절단, 16K면 0). `Reasoning: medium`·예산 문구는 두 독립 표본에서 무효 → math/generic one-shot은 11/13 채점된 v3.3로 복귀. 결과 나오면 키노트 10장 `final-score-value`·README에 기입.

# 🧾 최종 제출 세트 — v6.6 DIRECT + one-shot coding v3.7 · math/generic v3.3

🕒 **최신 반영: 2026-08-23 09:50 KST** — 이 페이지의 파일명이 곧 최종본. 이전 버전(v3.x~v7.1)과 프롬프트 전문은 [📚 아카이브 › 프롬프트 전체 이력 · 제출 초안 전문].

## 0. 한눈에

- 에이전트 **2** — **Conductor**(플래너) + **Solver**(사용자 정의), 전원 `furiosa-ai/gpt-oss-120b`, 도구 OFF, 메모리 OFF.
- 설계: **플래너가 곧 솔버.** 러너가 채점하는 텍스트는 플래너의 계획 단계 최종 메시지(H1)이므로 Conductor가 분해·위임·도구 없이 그 메시지에서 답한다. Solver는 0태스크 fan-out 때 같은 답을 한 번 더 내는 쌍둥이(H2 보험).
- 파일 위치: `~/Documents/Developer/jxc-selfeval/submissions/` · 제출 파일 = `~/Documents/Developer/bibimbap-squad/workspace/.squad.json`(GUI **Save** 때마다 재작성 — 저장 전엔 구버전).

## 1. 카드 복사 명령 (AI:GO 카드 편집창에 ⌘V → 두 장 끝나면 **Save**)

| 카드 | 시스템 프롬프트 | 설명(description, 선택) |
| --- | --- | --- |
| **Conductor** (플래너) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_conductor_v6.6.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_conductor_v6.txt` |
| **Solver** (사용자 정의) | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/card_solver_v6.0.txt` | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/desc_solver_v6.txt` |

⚠️ `desc_conductor_v6.txt`의 문구는 v6.2 시점("… delegates coding to Coder")이라 v6.3 DIRECT(위임 없음)와 어긋남. 설명은 평가에 전달되지 않는 로스터 표시용이므로 비워 두거나 한 줄로 고쳐 입력(`check_squad.py`는 "설명 비어있음"을 경고만 하고 판정은 ✅).

## 2. GUI 세팅 (카드 2장)

| 항목 | Conductor | Solver | 비고 |
| --- | --- | --- | --- |
| 역할 | **플래너** (Squad당 1개 · 제출 필수) | **사용자 정의** — 라벨 입력 필수(예: `Solver`) | 라벨이 비면 Check가 거부 |
| 모델 | `furiosa-ai/gpt-oss-120b` | `furiosa-ai/gpt-oss-120b` | 명시 선택, 비워두기 금지 |
| 시스템 프롬프트 | v6.6 (4,053자 = v6.3 본문 + SWE 패치 계약 불릿) | v6.0 | **GUI 한도 4,096자 — 초과분은 잘림**(`check_squad.py`가 ≥4096 경고) |
| 최대 토큰 | 8192 | 8192 | `settingsOverrides`(토큰·라운드)는 평가에 미전달 — 로컬 완주용 |
| 도구 | 전부 OFF (배지 0) | 전부 OFF | 평가는 tool-less — 켜져 있어도 Check 경고만, 점수 무관 |
| 메모리 | OFF | OFF | 기본 ON이므로 확인 |
| 실행 모드 | 인프로세스 | 인프로세스 | 컨테이너 불필요 |

## 3. 검증 게이트 (Save 후, 제출 전 — 순서대로)

| # | 무엇을 | 명령 / 행동 | 통과 기준 |
| --- | --- | --- | --- |
| 1 | GUI 1문항 완주 (generic) | `pbcopy < ~/Documents/Developer/jxc-selfeval/gui_generic_v66.txt` → 스쿼드 대시보드 요청창에 붙여넣기(자동 승인 ON) | **태스크 0** · Conductor 메시지 = `Answer: <LETTER>` 한 줄 · 경고 **"The planner replied but produced no tasks … Each agent was given the whole request instead" = 정상**(의도된 fan-out) · Solver도 같은 답. v6.3 실측: 양쪽 `Answer: B`, 3,381 tok · 38s |
| 2 | (선택) math · coding 완주 | `gui_math_v66.txt` · `gui_coding_v66.txt` | 동일 — 태스크 0, 답만 |
| 3 | 파일 판독 | `python3 ~/Documents/Developer/jxc-selfeval/check_squad.py` | `판정: ✅ v6.6 DIRECT (v6.3 + SWE 패치 계약) — 붙여넣기 가능` · 플래너 Conductor · 에이전트 2 · 모델 gpt-oss-120b ×2 · 저장 시각이 방금 |
| 4 | 복사 | `python3 ~/Documents/Developer/jxc-selfeval/check_squad.py --copy` | `📋 클립보드에 복사됨` |
| 5 | 폼 ① **Your squad's .squad.json** | ⌘V — 한 글자도 수정 금지 (Export 템플릿이 아니라 워크스페이스 파일) | — |
| 6 | 폼 ② One-shot — coding | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/oneshot_coding_v3.7.txt` | `{{TASK}}` 리터럴 1회, ≤32 KB |
| 7 | 폼 ③ One-shot — math | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/oneshot_math_v3.3.txt` | 〃 |
| 8 | 폼 ④ One-shot — generic | `pbcopy < ~/Documents/Developer/jxc-selfeval/submissions/oneshot_generic_v3.3.txt` | 〃 |
| 9 | **Check without submitting** | 무료·무제한, 큐 점유 0 | "No problems found" — 경고(settingsOverrides 미전달 · 도구 무효)는 정상 |
| 10 | **Submit to the queue** | 팀당 대기 1 · 실행 1 | 폼 상단 "Waiting 1 of 1" · 사이트 시간은 UTC(KST−9) · 큐는 배치 처리라 결과 시점 예측 불가 |

**one-shot v3.3 구조**: `[INSTRUCTION]`(분해·태스크·도구·위임 금지, 답 아닌 메시지 = 0점, 도구 결과를 봤어도 답) → `[HOW]`(트랙별 절차: 객관식 소거 / 경시 수학 정규화 / LCB·SWE 출력 형식) → `Solve this problem:` → `{{TASK}}` → `[OUTPUT]`(답만, PLAN READY 금지, 두 경로로 풀고 대조 후 답). 문제 **뒤에도** 지시를 두는 이유 = 최근성. 러너 조건 재현 측정: `selfeval.py --system-file card_conductor_v6.3.txt --planner-mode --oneshot-file oneshot_<track>_v3.3.txt`.

## 4. 제출 이력 (점수 = 리더보드 실측)

| 시각 (KST) | 스쿼드 | one-shot | 점수 | 판독 |
| --- | --- | --- | --- | --- |
| 8/22 14시 | v2 (8 agents, gpt-oss 플래너, 중복 분해) | v2 | 기록 없음 | 채점 경로·가중치 확인용 |
| 8/22 18:44 | v3.5 (5 agents, Qwen3 thinking 플래너) | v2.1 | **0.186** | coding 18.4 · math 7.7 · generic 29.8 — thinking이 0태스크 → 전원 fan-out |
| 8/22 19:35 | v3.7b (Qwen3 `/no_think` 플래너, 답만 출력 솔버) | v2.2 | **0.254** | 15.8 · 23.1 · 47.1 — 러너가 `/no_think` 무시(출력 2,547/회), ~60% 문항 fan-out |
| 8/23 01:53 | v7.1 앙상블 (Conductor + 3 솔버 + Judge DAG, 5 agents) | v4.0 | **0.045** | 0 · 0 · 17.9 — 로컬 정상 완주였는데 플래너 마지막 말 "PLAN READY" → **H1 확정** |
| 8/23 02:54 (17:54Z) 채점 | **v6.0 DIRECT** (Conductor 직접 풀이 + Solver) | v3.0 계열 | **0.285 (5위)** | 15.8 · **53.8 (1위 동률)** · 28.7 — generic 객관식 분해(802 요청 = 5.5/문항) |
| 8/23 03:1x (18:36Z 채점) | **v6.3 DIRECT** (분해 금지 + 자기일관성) | **v3.3** | **0.373 (4위)** | 18.4 · **53.8 (1위 동률, 11/13 채점)** · 58.6 — 322회 = 2.2/문항 · 뷰어 run-009
| 8/23 07:5x | **v6.4** (v6.3 + 마무리 규율 + SWE 정밀 diff) | **v3.5** (도메인 특성) | **0.363 (6위)** | **21.1 (= 8/38, LCB만)** · 46.2 (채점 7/13) · 56.8 — 긴 검증 지시 → 컷오프 증가
| 8/23 09:1x | **v6.6** (v6.3 본문 + SWE 패치 계약) | coding **v3.7** · math/generic **v3.3** | ⏳ 채점 대기 | SWE 패치 적용률·math 채점 수 회복이 관전 포인트 |

백업 파일: `submissions/2nd_v3.5_1844.squad.json` · `3rd_v3.7b.squad.json` · `4th_v4.0_2336.squad.json` · `6th_v7.1_0153.squad.json`. v4.0(Math `Reasoning: high`)·v5.1(3모델 혼합) 세트는 준비·저장됐으나 HQ 기록에 개별 점수 없음 — 구성은 아카이브 참조.
