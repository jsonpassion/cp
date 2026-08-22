# 🖥 AI:GO 사용 가이드

> 출처: [go.backend.ai/ko](https://go.backend.ai/ko/) 공식 매뉴얼 (2026-08-22 수집). 해커톤에 필요한 부분만 추려 정리. 전체 문서: [매뉴얼](https://go.backend.ai/ko/manual/) · [FAQ](https://go.backend.ai/ko/manual/reference/faq/)

## 설치 (v1.12.1)

| 방법 | 명령/링크 |
| --- | --- |
| macOS (Apple Silicon) | DMG 다운로드 또는 `brew tap lablup/tap && brew install --cask backend-ai-go` |
| Windows (x64) | 인스톨러 |
| Linux | AppImage (x64, ARM64) |
| CLI/헤드리스 | `curl -fsSL https://go.backend.ai/install.sh \| sh` |

## 모델 준비

**Hugging Face에서 받기**: 사이드바 **Browse**(🤗 아이콘) 탭 → 검색("Qwen" 등) → 포맷 필터(GGUF는 범용, **MLX는 Apple Silicon**) → 용량·양자화(Q4_K_M 등) 필터 → 다운로드. 여러 모델 동시 다운로드 가능, **다운로드** 탭에서 진행률 확인.

**로컬 파일 로드**: Models 탭 → Import → `.gguf` 드래그 앤 드롭.

**원격/클라우드 연결**: 설정의 **클라우드 통합** 섹션에서 OpenAI / Anthropic / Gemini / **OpenAI-호환 서비스** 연결 — 🔑 **대회 제공 RNGD 엔드포인트는 여기에 "OpenAI-호환"으로 등록**합니다 (dev key 사용).

## Squad 만들기 (이번 미션의 핵심 경로)

사이드바 **Squad** → **새 Squad** 클릭:

1. **이름·설명** 입력
2. **워크스페이스 디렉터리** 선택 — 존재하고 쓰기 가능하며 다른 Squad가 안 쓰는 경로여야 함. **⭐ 이 경로 루트에 `.squad.json`이 생성되고, Squad를 저장할 때마다 다시 써짐 — 제출 사이트에 붙여넣을 파일이 바로 이것**
3. **에이전트 구성** — 템플릿에서 시작 / **에이전트 추가** 수동 / Cowork 프로필 가져오기
   - 에이전트별 설정: **이름 / 역할 / 시스템 프롬프트 / 도구 / 메모리**
   - 역할: **플래너**(팀 조율·계획·태스크 배분, Squad당 1개) / 개발자 / 리뷰어(읽기 전용) / 작가 / 커스텀
   - ⚠️ **제출 규칙과 일치**: role에 "planner"가 포함된 에이전트가 반드시 1개 있어야 함
4. **만들기** — 워크스페이스가 자동 초기화됨

### 워크스페이스 구조

```
my-squad-workspace/
├── .squad.json          ← 제출용 (Squad 저장 시마다 갱신)
├── .squad-config.json   ← 복원용 매니페스트
├── plans/    tasks/    agents/(memory.md)    logs/
```

앱 안에 워크스페이스 탐색기(트리·미리보기·전문 검색)가 있음.

## Squad 실행

1. Squad 모니터링 대시보드에서 요청을 자연어로 입력 (auto-approval 토글 가능)
2. **플래너가 계획 생성** — 태스크·담당 에이전트·의존성·우선순위
3. 계획 승인(Approve) 또는 반려(Reject)
4. **Wave 실행** — 의존성 없는 태스크는 병렬, 실패한 태스크의 하위 태스크는 자동 skip
5. 실시간 모니터링: wave 진행, 태스크 상태, 에이전트 상태, **토큰 소비량**, 활동 피드
6. **Cancel**로 즉시 중단 가능 (완료된 결과는 보존)

💡 **예산 제어**: per-agent / per-task / 전체 토큰 한도를 걸 수 있음 — **토큰 효율 30점과 직결되는 기능이므로 반드시 활용**.

## Squad Template JSON (.squad.json)

- `schemaVersion: 1`, camelCase 필드
- 템플릿 레벨: `id`, `name`, `description`, `icon`, `category`, `suggestedModels`
- 에이전트 레벨: `name`, `role`, `systemPrompt`, `toolConfig`(enabledTools 등), `modelPreferences`, `memoryEnabled`
- Export/Import 버튼으로 JSON 내보내기/가져오기 가능 (로컬 검증 후 수락)

## 로컬 API 서버 (자체 채점 루프 연동)

AI:GO는 OpenAI 호환 API 서버를 내장:

- 기본 포트 **39080**, 기본 바인딩 127.0.0.1 → Base URL `http://127.0.0.1:39080/v1`
- 활성화: **API > General** → TCP 서버 켜기 (외부 접근은 "Allow External Access")
- 인증: **API > Access Keys**에서 키 생성 → **`X-API-Key` 헤더**로 전달

```bash
curl http://127.0.0.1:39080/v1/models -H "X-API-Key: YOUR_ACCESS_KEY"
```

→ `jxc-selfeval`을 이 주소로 돌리면 AI:GO에 연결된 모델(RNGD 엔드포인트 포함)로 연습 세트를 채점할 수 있음.


## 에이전트 실행 모드: 인프로세스 vs 컨테이너 (공식 문서 기준)

| | 인프로세스 (기본) | 컨테이너 |
| --- | --- | --- |
| 실행 위치 | AI:GO 호스트 프로세스 내 | 격리 Docker/Apple Container |
| 요구 | 없음 | 런타임 설치 + `aigo-agent-runner:latest` 이미지 + **그룹 네임스페이스** + 마운트 허용 목록 |
| 파일 접근 | 워크스페이스 직접 | `/workspace`(메인만 RW) + 허용 마운트만, `.ssh`·`.env`·`.aws` 차단 |
| 오버헤드 | 최소 | provisioning(이미지 준비·기동) |

**우리 결정: 5개 전부 인프로세스.** 평가는 tool-less·주최측 인프라 실행이라 컨테이너의 가치(격리 도구 실행)가 쓰일 자리가 없고, 설정 비용·실패 모드만 늘어남. 워크스페이스 단계의 "그룹 네임스페이스"는 컨테이너 모드 전용 필드 → 비워둠. 설정 위치: 에이전트 카드 → 실행 모드 드롭다운(컨테이너 선택 시 이미지·타임아웃·마운트 항목 노출).

## 트러블슈팅 (실전에서 겪은 것들)

| 증상 | 원인 | 해결 |
| --- | --- | --- |
| 프로바이더 연결 테스트 "라우터 실패" | Base URL 오입력 (`hub...:8446`은 연결 불가) | **`https://submission.jxc.events.lablup.ai:8445`** + 타입 vLLM |
| "The local router is not running" | 내장 라우터(continuum) 미기동 | 설정 **API > General → TCP 서버 활성화** (포트 39080 기본, 외부 액세스는 OFF 유지) |
| 헤드리스 aigo-server에서 "Cannot spawn router without an initialized runtime" → 스쿼드 0토큰 정지 | Continuum Router가 별도 미배포 상태라 헤드리스 환경에 런타임 없음 (라우터 상태 starting으로 래치, start/stop API가 no-op) | **데스크톱 모드 사용** (Lablup 공식 안내). deb 패키지는 8/22 오후 공유 예정 — 우리 팀은 데스크톱 모드라 무관 |
| 대형 문항을 채팅창에 붙여넣으면 글자수 제한 에러 | GUI 입력창 제한 (SWE 문항 66KB) | 채팅창은 실평가 경로 아님 — API 호출(selfeval)·Check·Test run으로 리허설 |

## 제출 폼 스펙 (제출 사이트 확정)

| 항목 | 규칙 |
| --- | --- |
| Squad 파일 | 워크스페이스 루트의 `.squad.json`을 **통째로, 수정 없이** 붙여넣기 |
| 크기 제한 | ≤ **1 MiB** (1,048,576 bytes), 에이전트 ≤ **50개** |
| 필수 역할 | role에 **"planner" 포함** 에이전트 1개 |
| one-shot prompt | 트랙별(coding/math/generic) 각 1개, **리터럴 `{{TASK}}` 필수**, 각 ≤ **32 KB** UTF-8 |
| Check | **무료·무제한** — 큐 점유 없음, 쿨다운 없음. Submit도 같은 검증을 먼저 통과해야 큐에 들어감 |
| 큐 제약 | 대기 1개 · 실행 1개 (팀당) |
| 시간 표기 | 사이트는 **UTC** 표시 (KST−9) |

⚠️ "Squad Template JSON은 제출물이 아님" — 템플릿이 아니라, **AI:GO에서 Squad를 실제로 만들어** 워크스페이스에 생성된 `.squad.json`을 내야 함.
