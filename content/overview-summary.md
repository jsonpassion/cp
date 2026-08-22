> 🏁 **08/23 08:32 제출용 리포 완성**: https://github.com/jsonpassion/junction2026-54-1-CouchPotato — `keynote/keynote.pdf`(5분 영어 11장 + `script.md` 한글병기 대본) · `viewer/kids.html`(최종 DIRECT 파이프라인 그림책, 시연용: https://jsonpassion.github.io/junction2026-54-1-CouchPotato/viewer/kids.html) · `viewer/viewer.html`(원장/간단) · `squad/`(최종 카드·one-shot v3.5·squad.json) · README(3축 정렬). 플랫폼 폼: Punchline/Description은 최종 제출 탭, 팀원 이름은 키노트 11장에 기입.

# 🏠 대시보드 — [541] BIBIMBAP (팀 CouchPotato 54-1)

🕒 **최신 반영: 2026-08-23 03:20 KST** — 이 페이지는 "지금 어디에 있고 오늘 뭘 하나"만 담습니다. 근거는 각 탭에.

- **리더보드**: 우리 ****0.373 (4위)** · 5위** (v6.0 DIRECT 런, 8/22 17:54Z) — coding 15.8 · **math 53.8 (1위 동률)** · generic 28.7. 1위 MISHULTA 0.426 (gpt-oss 단일, 2.2회/문항) · 2위 TheresNoFree 0.403 · 3위 DemoDayCare 0.393. 점수 = 트랙 정확도 가중 평균만(coding .50 · math .25 · generic .25, 문항 38/13/96) — 토큰·시간은 동점 처리, 캡 없음.
- **6차 제출(03:1x) 채점 대기 = v6.3 DIRECT + one-shot v3.3**: generic이 새던 원인(객관식에서 플래너가 태스크로 분해, 802 요청 = 5.5/문항)에 처방한 버전. 요청 수가 ≈2/문항으로 내려오고 generic이 회복되면 성공.
- **최종 스쿼드 = 에이전트 2, 전원 `furiosa-ai/gpt-oss-120b`, Conductor(플래너)가 직접 답한다** — `card_conductor_v6.3.txt`(분해·위임·도구 금지 + 두 경로 풀이 자기일관성, 3,393자) + `card_solver_v6.0.txt`(0태스크 fan-out 쌍둥이) + `oneshot_{coding,math,generic}_v3.3.txt`(트랙별 절차 + 문제 앞뒤로 "분해 금지·답만"). 복사 명령·GUI 세팅·검증 게이트 → [🧾 최종 제출].
- **핵심 증거 H1**: 러너가 채점하는 텍스트 = **플래너의 계획 단계 최종 메시지**. 서브에이전트 출력은 점수에 닿지 않는다(v7.1 앙상블 0.045, 상위 3팀 공통 플래너 직접 답) → [🔍 핵심 인사이트].
- **한 줄 메시지**: **"관측할 수 있으면 줄일 수 있다."** 관측(Trace) → 절감(26,808 → 1,611 tok, ÷17) → 재투자 실험(앙상블 12,544 tok → 0.045) → 다시 관측(누구의 마지막 말이 채점되는가) → **단순화**(2 에이전트 DIRECT → 0.285).
- **산출물(전부 푸시됨)**: 리포 https://github.com/jsonpassion/bibimbap · 데모 https://jsonpassion.github.io/bibimbap/ (`viewer/viewer.html` v3.3 · `viewer/kids.html`) · 피치덱 `bibimbap/deck/deck.pdf`(12p) · 요약 `deck/summary.md`(1,196자) · 대본 `deck/speaker-notes.md`(4분).

## 오늘 체크리스트 (8/23 KST)

| 시각 | 할 일 | 완료 기준 |
| --- | --- | --- |
| 지금~ | **v6.3 런 결과 판독** — Runs 탭에서 트랙별 정확도 · 요청 수 · 모델별 토큰 | generic 회복 여부, 요청 ≈2/문항 |
| 판독 직후 | 덱 9장 `final-score-value`에 최종 점수 · 12장 `team-members` 팀원 이름 → `deck.pdf` 재생성 | PDF 12p, 숫자 = 리더보드 |
| (여유 시) | coding(.50) 개선 — SWE 절차 강화 변형은 **러너 조건 LCB 20 회귀 확인 후에만** 적용 | 회귀 없음 |
| **~11:30** | **플랫폼 제출**: deck.pdf(Google Drive "링크 있는 모든 사용자" — 시크릿 창 확인) · summary.md · 리포 URL · 데모 URL · status **final** 확인 · Discord `✅-confirm-submission` 스크린샷 · 별도 폼(강력 권장) | 공식 마감 12:00 — 남은 30분은 버퍼 |
| 13:00–16:00 | Demo Expo — 뷰어 `D`(자동 루프) 켜두고 방문자에게 스크러버를 직접 만지게 (참가자 투표 60%) | 네트워크 불필요 |
| 16:00–17:00 | Final PT (4분, `speaker-notes.md`) → **17:00–17:30 Peer Review 전원 필수 제출** | |
