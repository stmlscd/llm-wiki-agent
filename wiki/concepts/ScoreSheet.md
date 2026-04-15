---
title: "ScoreSheet (KTT 표준 기록지)"
type: concept
tags: [score-sheet, print, ktt, a4, jinja2]
sources: [52nd-president-cup]
last_updated: 2026-04-11
---

# ScoreSheet (KTT 표준 기록지)

한국탁구협회(KTT) 공식 규격을 준수하는 A4 기록지. [[RoundRobinLeague]] 예선 기록지와 [[TournamentBracket]] 본선 기록지 두 종류가 있다.

## 기록지 규격

| 항목 | 사양 |
|------|------|
| 용지 | A4 세로 (210 × 297mm) |
| 여백 | `@page { margin: 10mm 8mm }` |
| 레이아웃 | 27열 그리드 |
| 경기당 높이 | ~275mm (A4 277mm 여백 내 맞춤) |

## 기록지 구성 요소

### 상단 헤더
- 날짜, 테이블 번호, 부문, 조
- 두 선수 정보 섹션 (좌·우)

### 경기 행 (7행)
- 각 게임별 점수 입력 셀
- 5경기(게임) 구성이 표준

### 하단
- 심판 서명란
- 감독(지도자) 서명란
- 비고란

## 앞장 (리그표, 프론트 페이지)

예선 리그 결과 요약 시트. 두 섹션으로 구성:

**섹션 1 — 결과 매트릭스**
- 행·열: 선수 시드번호
- 대각선: CSS 사선 처리 (`linear-gradient`)
- 셀: "3:1", "0:3" 형태의 세트 스코어

**섹션 2 — 경기 일정표**
- 5인 조: 2열 레이아웃 (10경기를 좌5·우5 배치, A4 초과 방지)
- 4인 이하: 단열 배치
- 경기당 5행 (5게임 점수 입력)

## 기술적 주의사항

- Jinja2 연산자 우선순위: `schedule|length // 2` 오파싱 → `{% set %}` 별도 사용
- 5인 조 레이아웃: 10경기 × 5행 = 50행 → A4 초과 → 2열로 해결
- 인쇄 전용 CSS: `@media print` 블록에서만 적용

## 단체전 기록지 (print_team_scoresheet.html) — 2026-04-11 확정 구조

교보생명컵 기준으로 전면 재작성. **경기당 5개 sub-row** 방식이 표준.

| 요소 | 높이 |
|------|------|
| 대회명 헤더 | 7mm |
| TEAM SCORE SHEET 행 | 8mm |
| Event/Stage 행 | 9mm |
| Team vs Team 행 | 9mm |
| 컬럼 헤더 | 8mm |
| 경기 1개 (5 sub-row × 5.5mm) | 27.5mm |
| 경기 7개 합계 | 192.5mm |
| Adviser / Winner / Signature (각 10mm) | 30mm |
| **합계** | ~284mm (A4 margin 6mm 기준 적합) |

### 핵심 구조 결정
- **선수명 + 승수박스**: `colspan=5 rowspan=5` 단일 셀 — 선수명(위) + 승수박스(아래)
- **Adviser/Winner/Signature**: 반드시 단일 `<tbody>` 안에 연속 배치. 별도 tbody + bl-body-td 구분선 사용 시 브라우저가 테이블을 분리 렌더링해 레이아웃 깨짐
- **"Colour of playing shirt"**: 헤더에서 `<br>` 포함 2줄 표시
- **게임 format**: 16강 이상 자동 생성 매치는 `game_format` 명시적 UPDATE 필요 (기본값=전부 단식)

## API

```
GET /print/scoresheet/<cat>/<grp>/<match_num>   → 단일 기록지
GET /print/scoresheet-group/<cat>/<grp>          → 그룹 전체 기록지
GET /print/league/<cat>/<grp>                    → 리그 앞장
GET /t-scoresheet/<cat>/<round>/<num>            → 본선 단일 기록지
GET /t-scoresheet/<cat>/<round>/all              → 본선 라운드 전체
```

## 구현 파일

- `dist/templates/print_scoresheet.html` — 예선 기록지
- `dist/templates/print_league.html` — 리그 앞장
- `dist/static/css/print.css` — 인쇄 전용 스타일
- `dist/templates/stage2/scoresheet.html` — 본선 기록지

## Connections

- [[RoundRobinLeague]] — 예선 기록지 사용
- [[TournamentBracket]] — 본선 기록지 사용
- [[TeamMatch]] — 단체전 기록지 사용
- [[KETTF]] — KTT 표준 형식 기준
- [[52nd-president-cup]] — 구현 소스
