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
| Description * (5000자, 마크다운) | 아래 최종본(= deck/summary.md) |
| Project Demo | https://jsonpassion.github.io/junction2026-54-1-CouchPotato/viewer/kids.html (그림책·무한 루프 시연) · `…/viewer/viewer.html` |
| Source Code | https://github.com/jsonpassion/junction2026-54-1-CouchPotato (public; 작업 이력은 github.com/jsonpassion/bibimbap) |
| Presentation | `junction2026-54-1-CouchPotato/keynote/keynote.pptx` (영문 18장, 편집 가능) · PDF 필요 시 `keynote.pdf`(18p) — Google Drive **"링크가 있는 모든 사용자"** 공개 링크 (시크릿 창에서 열어 확인) 또는 리포 링크 https://github.com/jsonpassion/junction2026-54-1-CouchPotato/blob/main/keynote/keynote.pptx |
| Project Summary | `bibimbap/deck/summary.md` (1,303자, 09:45 갱신) |
| Video | (여유 시) 뷰어 리플레이 30초 녹화 |

제출 후: status **final** 확인 → Discord `✅-confirm-submission` 채널에서 반영 확인(스크린샷) → 별도 제공 폼에도 제출(필수 아님, 강력 권장).

## Punchline * — ✅ 최종 (08/23 03:34)

```
관측할 수 있으면 줄일 수 있다 — 손에 쥘 수 있는 모델로 만든 스쿼드의 모든 판단을 Trace로 되감고, 43배 낭비를 ÷17로 걷어낸 뒤 채점 경로까지 읽어내 단순화한 팀.
```
(99자)

## Description * — ✅ 최종 (08/23 09:45, `bibimbap/deck/summary.md`와 동일)

```
[541] BIBIMBAP — 관측할 수 있으면 줄일 수 있다

손에 쥘 수 있는 모델(FuriosaAI RNGD 위 gpt-oss-120b)로 AI:GO 에이전트 스쿼드를 만들고, 그 스쿼드의 모든 판단을 Trace로 되감아 보게 했습니다.

첫 스쿼드는 같은 수학 문항에 단독 모델(617 tok·5.09 s)의 43배인 26,808 tok·60.2 s를 썼습니다. 로그를 해부하자 낭비는 세 층이었습니다 — 플래너의 동일 태스크 3중 생성, 태스크당 최대 11회의 루프 공회전(총 28회 호출), 비용의 91%를 차지한 입력 재전송. 루프 규약을 역공학하고, 로컬 로그에 없던 플래너 thinking 비용을 라우터 통계로 찾아 /no_think(1,229→54 tok)로 잘라내자 같은 문항이 1,611 tok·17 s(÷17)가 됐습니다.

솔버 프롬프트는 공개 연습 세트 전수로 검증했습니다: generic 140문항 79.1→80.9%(출력 토큰 −16%), math 164문항 79.8→82.6%(−21%), LCB 20문항 75.0→88.9%(−15%), AIME-2024는 Reasoning: high로 72.4→78.6%. Qwen3와 EXAONE은 측정 후 제외했고, 20문항 베이스라인 60%가 표본 불운이었음도 전수로 정정했습니다.

가장 큰 교훈은 리더보드였습니다. 로컬에선 정답을 내던 3 솔버+Judge 앙상블이 0.045 — 채점기는 계획 단계 플래너의 최종 메시지만 읽습니다(상위 3팀 모두 gpt-oss ≈ 1회/문항). 최종 스쿼드는 2 에이전트(전원 gpt-oss): Conductor가 0 태스크·0 도구로 직접 답을 쓰고, 같은 페르소나의 Solver가 fan-out 보험을 섭니다 → 0.373(math 53.8% 1위 동률). 마지막 발견은 코딩 21.1% = 8/38 = LCB만 정답, SWE 전멸의 원인이 풀이가 아니라 패치 형식(빈 @@ 헤더)이라는 것 — 채점기 허용 범위를 실측해 패치 계약(3+3 컨텍스트, 세어 쓴 hunk 헤더)을 프롬프트에 넣었습니다(숫자 헤더 0/8→8/8, LCB 회귀 20/20).

산출물: squad.json + one-shot 3종, 5분 키노트(18장), Trace Viewer(간단/원장 모드, 루브릭 6축 1:1, 원본 events.jsonl 재생), 그림책 kids.html(실전 재생·무한 루프). 리포 github.com/jsonpassion/junction2026-54-1-CouchPotato · 데모 jsonpassion.github.io/junction2026-54-1-CouchPotato/viewer/kids.html
```
(1303자 · 5000자 한도)

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
