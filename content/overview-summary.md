> 🏁 **08/23 09:50 — 9차(최종) 리더보드 제출 완료(v6.6 세트) · 제출용 리포 마감 정리 중**: https://github.com/jsonpassion/junction2026-54-1-CouchPotato — `keynote/keynote.pdf`(5분 영어, **18장**, 에디토리얼 테마 + `script.md` 한글병기) · `viewer/kids.html`(시연용: 스텝 느리게·무한 반복) · `viewer/viewer.html` · `squad/`(v6.6 카드·one-shot coding v3.7/math·generic v3.3·squad.json) · README(결과 궤적·발견 2건). 플랫폼 폼(~11:30): Punchline/Description = `bibimbap/deck/summary.md`(갱신본), 피치덱 PDF = `keynote/keynote.pdf`, 리포 URL, 데모 URL kids.html.

# 🏠 대시보드 — [541] BIBIMBAP (팀 CouchPotato 54-1)

🕒 **최신 반영: 2026-08-23 09:50 KST** — 이 페이지는 "지금 어디에 있고 오늘 뭘 하나"만 담습니다. 근거는 각 탭에.

- **리더보드**: 최고 **0.373 (4위, 7차 v6.3 + one-shot v3.3)** — coding 18.4 · **math 53.8 (1위 동률)** · generic 58.6, 2.2회/문항. 8차(v6.4 + v3.5) **0.363 (6위)**: coding **21.1** · math 46.2(채점 7/13) · generic 56.8. 1위 MISHULTA 0.426 · 2위 0.403 · 3위 0.393 (전원 gpt-oss 단일 플래너 ≈2.2회/문항). 점수 = 가중 정확도만(coding .50 · math .25 · generic .25, 문항 38/13/96).
- **9차(최종) 제출 = v6.6 세트, 채점 대기**: `card_conductor_v6.6.txt`(v6.3 본문 + SWE 패치 계약, 4,053자) + `oneshot_coding_v3.7.txt`(패치 계약) + `oneshot_math_v3.3.txt` + `oneshot_generic_v3.3.txt`. 명령·게이트·이력 → [🧾 최종 제출].
- **발견 ①**: 코딩 21.1% = 8/38 = LCB만 정답, **SWE 전멸은 패치 형식 탓**(빈 `@@` 헤더 → git apply 거부). 계약 적용 시 숫자 헤더 8/8, LCB 회귀 20/20, 로컬 적용 1~2/8. **발견 ②**: math 미채점 = 러너 cap에 잘린 추론; 프롬프트로 추론 길이 제어 불가(2표본) → v3.3 복귀. 상세 → [🔍 핵심 인사이트 7·8].
- **핵심 증거 H1**: 러너가 채점하는 텍스트 = **플래너의 계획 단계 최종 메시지**(v7.1 앙상블 0.045, 상위 3팀 공통 플래너 직접 답) → [🔍 핵심 인사이트].
- **한 줄 메시지**: **"관측할 수 있으면 줄일 수 있다."** 관측(Trace) → 절감(26,808 → 1,611 tok, ÷17) → 재투자 실험(앙상블 → 0.045) → 다시 관측(누구의 마지막 말이 채점되는가 · 어떤 패치가 적용되는가) → **단순화**(2 에이전트 DIRECT → 0.373).
- **산출물(전부 푸시됨)**: 제출 리포 https://github.com/jsonpassion/junction2026-54-1-CouchPotato (키노트 18장 PDF · 그림책 · 뷰어 · squad) · 작업 리포 https://github.com/jsonpassion/bibimbap · 데모 https://jsonpassion.github.io/junction2026-54-1-CouchPotato/viewer/kids.html

## 오늘 체크리스트 (8/23 KST)

| 시각 | 할 일 | 완료 기준 |
| --- | --- | --- |
| 지금~ | **9차(v6.6) 런 결과 판독** — 트랙별 정확도 · math 채점 수(13 중) · coding이 21.1%를 넘는지(SWE 적용) | 결과를 키노트 10장·README·HQ에 기입 |
| 판독 직후 | 키노트 10장 `final-score-value` 점수 · 18장 `team-members` 팀원 이름 → `keynote.pdf` 재생성 | PDF 18p, 숫자 = 리더보드 |
| (여유 시) | coding(.50) 개선 — SWE 절차 강화 변형은 **러너 조건 LCB 20 회귀 확인 후에만** 적용 | 회귀 없음 |
| **~11:30** | **플랫폼 제출**: deck.pdf(Google Drive "링크 있는 모든 사용자" — 시크릿 창 확인) · summary.md · 리포 URL · 데모 URL · status **final** 확인 · Discord `✅-confirm-submission` 스크린샷 · 별도 폼(강력 권장) | 공식 마감 12:00 — 남은 30분은 버퍼 |
| 13:00–16:00 | Demo Expo — 뷰어 `D`(자동 루프) 켜두고 방문자에게 스크러버를 직접 만지게 (참가자 투표 60%) | 네트워크 불필요 |
| 16:00–17:00 | Final PT (4분, `speaker-notes.md`) → **17:00–17:30 Peer Review 전원 필수 제출** | |
