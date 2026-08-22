# 🖥 AI:GO 사용 가이드

> 출처: [go.backend.ai/ko](https://go.backend.ai/ko/) 공식 매뉴얼 (2026-08-22 수집) + 실전에서 확인한 한도·동작(2026-08-23). 해커톤에 필요한 부분만 추려 정리. 전체 문서: [매뉴얼](https://go.backend.ai/ko/manual/) · [FAQ](https://go.backend.ai/ko/manual/reference/faq/)

## ⚠️ 실전에서 확인한 한도·규칙 (먼저 읽을 것)

| 항목 | 사실 | 대응 |
| --- | --- | --- |
| **시스템 프롬프트 4,096자** | GUI 카드의 시스템 프롬프트는 4,096자까지만 저장 — **초과분은 잘림**(경고 없음) | 카드 파일을 4,096자 이내로(v6.3 = 3,393자) · `check_squad.py`가 ≥4096을 경고 |
| **사용자 정의 역할 라벨 필수** | 역할을 '사용자 정의'로 두고 라벨을 비우면 제출 사이트 Check가 거부 | Solver 카드에 라벨(예: `Solver`) 입력 · `check_squad.py`가 공란 검사 |
| 평가에 전달되지 않는 것 | `settingsOverrides`(최대 토큰·도구 호출 라운드) · `toolConfig.enabledTools`(평가는 tool-less) — Check 경고로 확정 | 경고는 무시, 점수 무관 |
| **"planner replied but produced no tasks" 경고** | 플래너가 태스크 0개 → 런타임이 요청 전체를 **비플래너 전원에게 배포(fan-out)** — 메시지: *"The planner replied but produced no tasks… Each agent was given the whole request instead"* | **DIRECT 설계에서는 정상**(플래너 답 + Solver 쌍둥이). 로스터가 2장이라 fan-out 비용 = 1회 |
| 빈 응답 | 본문이 비면 "continuing…" 재시도, 도구 호출 없는 본문 응답이 루프 종료 | 프롬프트에 "본문을 비우지 말 것" |
| 로컬 "2회 호출" | GUI에서 도구가 켜져 있으면 도구 호출 시도 → 라운드 한도 → 도구 없이 재호출 | 도구 전부 OFF(배지 0)면 1회 |
| 답 텍스트 | 에이전트 응답 본문은 `logs/`·`tasks/`에 저장되지 않음(GUI 활동 피드 메모리만) | 뷰어는 "답 텍스트 없음"을 명시, 답 확인은 GUI 화면에서 |
| 실행 로그 형식 | 고정(운영진 확인) — `logs/events.jsonl` 키/값 커스터마이즈 불가 | 뷰어가 원본을 그대로 읽어 정규화 |
| `/no_think` | GUI·직접 API에서는 작동, **평가 러너에서는 무시됨** | 지시는 one-shot 끝줄이 더 견고 |
| dev key 레이트리밋 | 60 rpm · 입력 120K / 출력 40K tpm | selfeval에 429 백오프 |

## 설치 (v1.12.1)

| 방법 | 명령/링크 |
| --- | --- |
| macOS (Apple Silicon) | DMG 다운로드 또는 `brew tap lablup/tap && brew install --cask backend-ai-go` |
| Windows (x64) | 인스톨러 |
| Linux | AppImage (x64, ARM64) |
| CLI/헤드리스 | `curl -fsSL https://go.backend.ai/install.sh \| sh` (⚠️ 헤드리스는 라우터 런타임 없음 — 아래 트러블슈팅) |

## 모델 준비

**원격/클라우드 연결**: 설정의 **클라우드 통합** 섹션에서 **OpenAI-호환 서비스** 연결 — 🔑 **대회 제공 RNGD 엔드포인트 `https://submission.jxc.events.lablup.ai:8445`(타입 vLLM)를 여기에 등록**(dev key 사용). 모델 ID: `furiosa-ai/gpt-oss-120b` · `furiosa-ai/Qwen3-32B-FP8` · `furiosa-ai/K-EXAONE-236B-A23B-NVFP4A16`.

(참고) Hugging Face에서 받기: 사이드바 **Browse** → 검색 → 포맷(GGUF 범용 / MLX Apple Silicon) → 다운로드. 로컬 파일: Models → Import → `.gguf` 드롭. 이번 미션에는 불필요.

## Squad 만들기 (이번 미션의 핵심 경로)

사이드바 **Squad** → **새 Squad** 클릭:

1. **이름·설명** 입력
2. **워크스페이스 디렉터리** 선택 — 존재하고 쓰기 가능하며 다른 Squad가 안 쓰는 경로. **⭐ 이 경로 루트에 `.squad.json`이 생성되고, Squad를 저장(Save)할 때마다 다시 써짐 — 제출 사이트에 붙여넣을 파일이 바로 이것** (우리: `~/Documents/Developer/bibimbap-squad/workspace/.squad.json`)
3. **에이전트 구성** — **에이전트 추가**로 수동 구성(템플릿·Cowork 프로필 가져오기도 가능)
   - 에이전트별 설정: **이름 / 역할 / 시스템 프롬프트(≤4,096자) / 모델 / 최대 토큰 / 도구 / 메모리 / 실행 모드 / 설명**
   - 역할: **플래너**(Squad당 1개 — 제출 필수) / 개발자 / 리뷰어(읽기 전용) / 작가 / **사용자 정의(라벨 필수)**
   - ⚠️ 모델은 명시 선택(비워두기 금지), 도구는 기본 ON이므로 전부 OFF(배지 0), 메모리 기본 ON이므로 OFF
4. **만들기** — 워크스페이스가 자동 초기화됨. 편집 후에는 **반드시 Save** — 저장 전엔 `.squad.json`이 구버전 그대로

### 워크스페이스 구조

```
my-squad-workspace/
├── .squad.json          ← 제출용 (Squad 저장 시마다 갱신)
├── .squad-config.json   ← 복원용 매니페스트
├── plans/    tasks/    agents/(memory.md)    logs/events.jsonl (원본 trace)
```

앱 안에 워크스페이스 탐색기(트리·미리보기·전문 검색)가 있음. `.squad.json`은 숨김 파일이라 Finder에선 ⌘⇧. 로 표시.

## Squad 실행

1. Squad 모니터링 대시보드에서 요청을 자연어로 입력 (**자동 승인 ON** — 스쿼드 저장 후 OFF로 돌아가는 경우 있음)
2. **플래너가 계획 생성** — 태스크·담당 에이전트·의존성·우선순위 (`create_task(title, description, assigned_to, depends_on, priority)`). 태스크 0개면 fan-out(위 표)
3. 계획 승인(Approve) 또는 반려(Reject)
4. **Wave 실행** — 의존성 없는 태스크는 병렬, 의존 태스크는 "Context from previous tasks"로 선행 결과 수신, 실패한 태스크의 하위 태스크는 자동 skip
5. 실시간 모니터링: wave 진행, 태스크 상태, 에이전트 상태, **토큰 소비량**, 활동 피드(답 텍스트는 여기서만 보임)
6. **Cancel**로 즉시 중단 가능 (완료된 결과는 보존)

GUI 1문항 리허설 요청문: `~/Documents/Developer/jxc-selfeval/gui_{generic,math,coding}_v63.txt` (pbcopy → 요청창). 판독: `bibimbap/viewer/analyze_run.py`.

## Squad Template JSON vs `.squad.json`

- Export/Import 버튼의 **Squad Template JSON**(`schemaVersion: 1`, `suggestedModels` 등)은 **제출물이 아님**
- 제출물은 워크스페이스 루트 **`.squad.json`**: `{squadId, squadName, initializedAt, appVersion, config: {agents: [...]}}` — 에이전트 배열은 `config.agents`, 각 에이전트는 `name` · `role`(`{type: planner}` / custom+라벨) · `systemPrompt` · `description` · `modelPreferences.preferredModelId` · `memoryEnabled`(전달됨) + `settingsOverrides` · `toolConfig`(평가 미전달)

## 로컬 API 서버 (자체 채점 루프 연동)

AI:GO는 OpenAI 호환 API 서버를 내장:

- 기본 포트 **39080**, 기본 바인딩 127.0.0.1 → Base URL `http://127.0.0.1:39080/v1`
- 활성화: **API > General** → TCP 서버 켜기 (외부 접근은 "Allow External Access" — OFF 유지)
- 인증: **API > Access Keys**에서 키 생성 → **`X-API-Key` 헤더**로 전달

```bash
curl http://127.0.0.1:39080/v1/models -H "X-API-Key: YOUR_ACCESS_KEY"
```

→ 실제 측정(`jxc-selfeval`)은 dev key로 RNGD 엔드포인트를 직접 호출. 러너 조건 재현: `selfeval.py --system-file <card> --planner-mode --oneshot-file <oneshot>`.

## 에이전트 실행 모드: 인프로세스 vs 컨테이너 (공식 문서 기준)

| | 인프로세스 (기본) | 컨테이너 |
| --- | --- | --- |
| 실행 위치 | AI:GO 호스트 프로세스 내 | 격리 Docker/Apple Container |
| 요구 | 없음 | 런타임 설치 + `aigo-agent-runner:latest` 이미지 + **그룹 네임스페이스** + 마운트 허용 목록 |
| 파일 접근 | 워크스페이스 직접 | `/workspace`(메인만 RW) + 허용 마운트만, `.ssh`·`.env`·`.aws` 차단 |
| 오버헤드 | 최소 | provisioning(이미지 준비·기동) |

**우리 결정: 2개 전부 인프로세스.** 평가는 tool-less·주최측 인프라 실행이라 컨테이너의 가치(격리 도구 실행)가 쓰일 자리가 없음. 워크스페이스 단계의 "그룹 네임스페이스"는 컨테이너 모드 전용 필드 → 비워둠.

## 트러블슈팅 (실전에서 겪은 것들)

| 증상 | 원인 | 해결 |
| --- | --- | --- |
| 프로바이더 연결 테스트 "라우터 실패" | Base URL 오입력 (`hub...:8446`은 연결 불가) | **`https://submission.jxc.events.lablup.ai:8445`** + 타입 vLLM |
| "The local router is not running" | 내장 라우터(continuum) 미기동 | 설정 **API > General → TCP 서버 활성화** (포트 39080 기본, 외부 액세스는 OFF 유지) |
| 헤드리스 aigo-server에서 "Cannot spawn router without an initialized runtime" → 스쿼드 0토큰 정지 | Continuum Router가 별도 미배포 상태라 헤드리스 환경에 런타임 없음 | **데스크톱 모드 사용** (Lablup 공식 안내) |
| `invalid input: Squad '…' already has 5 active executions` | 승인 대기·취소 직후 실행이 메모리에 활성으로 남음 — 저장 후 자동 승인 토글이 OFF로 돌아가 요청마다 대기 상태로 쌓임 | ① 대시보드에서 대기 항목 **Reject/Cancel** ② **긴급 정지(Emergency Stop)** ③ 그래도 안 되면 ⌘Q 재시작(완주 기록은 history.json에 보존). 이후 **자동 승인 ON** 확인 |
| 대형 문항을 채팅창에 붙여넣으면 글자수 제한 에러 | GUI 입력창 제한 (SWE 문항 66KB) | 채팅창은 실평가 경로 아님 — API 호출(selfeval)·Check로 리허설 |
| 프롬프트 끝부분이 사라짐 | 시스템 프롬프트 **4,096자** 한도에서 잘림 | 카드 파일 길이 확인(`wc -m`) · `check_squad.py` |
| Check: role label 관련 거부 | 사용자 정의 역할 라벨 공란 | 카드 역할에 라벨 입력 후 Save |
| "planner replied but produced no tasks…" 경고 | 플래너 0태스크 → fan-out | DIRECT 설계에서는 **정상** — Solver가 같은 답을 한 번 더 냄 |
| 저장했는데 `check_squad.py`가 구버전 | Save 전에 실행했거나 다른 워크스페이스 | 저장 시각 확인 후 재실행 |

## 제출 폼 스펙 (제출 사이트 확정)

| 항목 | 규칙 |
| --- | --- |
| Squad 파일 | 워크스페이스 루트의 `.squad.json`을 **통째로, 수정 없이** 붙여넣기 (`check_squad.py --copy`) |
| 크기 제한 | ≤ **1 MiB** (1,048,576 bytes), 에이전트 ≤ **50개** |
| 필수 역할 | role에 **"planner" 포함** 에이전트 1개 · 사용자 정의 역할은 라벨 필수 |
| one-shot prompt | 트랙별(coding/math/generic) 각 1개, **리터럴 `{{TASK}}` 필수**, 각 ≤ **32 KB** UTF-8 |
| Check | **무료·무제한** — 큐 점유 없음, 쿨다운 없음. Submit도 같은 검증을 먼저 통과해야 큐에 들어감. 경고(settingsOverrides·도구)는 정상 |
| 큐 제약 | 대기 1개 · 실행 1개 (팀당), 배치 처리 |
| 시간 표기 | 사이트는 **UTC** 표시 (KST−9) |
