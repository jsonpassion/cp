# ⚡ JunctionX Korea 2026 HQ

JunctionX Korea 2026 (HACK OUR ORIGIN, POSTECH, 8/21–8/23) 참가 팀의 정보 허브.
현장 슬라이드·배포물·트랙 세션 내용을 탭/서브탭 구조로 정리한 정적 웹앱입니다.

**Live**: https://jsonpassion.github.io/junctionx-korea-2026-hq/

## 구성

- 순수 정적 사이트 — 빌드 없음. `index.html` + `content/*.md`
- 마크다운 렌더링: [marked](https://github.com/markedjs/marked) (CDN)
- 다이어그램: [Mermaid](https://mermaid.js.org/) — 마크다운 안의 ```` ```mermaid ```` 코드블록이 자동 렌더링됩니다
- 헤더에 마감(킥오프/파이널/PT) 실시간 카운트다운

## 콘텐츠 누적 방법

1. `content/`에 새 마크다운 파일 추가 (예: `track-notes.md`)
2. `content/manifest.json`의 해당 탭 `subtabs` 배열에 한 줄 추가:
   ```json
   { "id": "notes", "label": "메모", "file": "track-notes.md" }
   ```
   새 최상위 탭이 필요하면 `tabs` 배열에 같은 구조로 추가
3. 커밋 & 푸시하면 GitHub Pages에 자동 반영 (캐시 갱신은 `manifest.json`의 `version` 올리기)

## 로컬 미리보기

```bash
python3 -m http.server 8000
```

후 http://localhost:8000 접속 (`file://`로는 fetch가 막혀서 안 됩니다).

---

비공식 팀 내부 정리 문서입니다. 공식 정보는 JUNCTION Platform / Discord 공지가 우선합니다.
