---
title: "대진표 어그러짐 수정 및 64강 한 장 출력"
type: source
tags: [bracket, gracket-lite, print, alignment, tt-result]
date: 2026-04-18
source_file: VPS 직접 수정
---

## Summary

tt-result 서버의 대진표 렌더링 어그러짐을 수정하고, 혼합복식 64강 대진표를 2장에서 1장으로 통합했다. 후속 라운드의 빈 매치에 플레이스홀더를 채워 대진표 균형을 맞췄다.

## Key Claims

- gracket-lite.js의 `getGameHeight()`가 첫 게임 하나의 높이만 읽어서 복식(이름 2줄) 시 어그러짐 발생
- 수정: 1라운드 전체 게임 최대 높이를 측정하여 통일 (`_normalizeGameHeights`, `fixedGameHeight`)
- 64강 인쇄: 32강×2장 분할 코드 제거, `bracket_size=64` 한 장 출력 (A4 가로)
- 빈 라운드: API(`compat_52nd.py`)에서 기대 매치 수 계산, 플레이스홀더 자동 생성
- 세부 기록(세트 스코어) 추가 시 높이 차이는 자연스러운 현상으로 수용

## 수정 파일 (VPS 직접 배포)

| 파일 | 수정 내용 |
|------|----------|
| `static/js/gracket-lite.js` | `_normalizeGameHeights()`, `_padRounds()`, `fixedGameHeight` 추가 |
| `app/routers/compat_52nd.py` | bracket_data API에 빈 매치 플레이스홀더 생성 |
| `app/routers/print_routes.py` | 64강 2장 분할 → 한 장 출력으로 변경 |
| `app/templates/print_bracket.html` | `is64` 플래그, 높이 1344px, A4 가로, 축소 폰트 |

## DB 변경

- `tournament_info` id=2 name: "릴리어스 제44회 한국대학탁구연맹전 및 2026 한.일 대학탁구대회 선발전" → "릴리어스 제44회 한국대학탁구연맹전"

## Connections

- [[TournamentBracket]] — 대진표 렌더링 개선
- [[TTResultRules]] — 규칙 6 (256강 대진표 출력 기반)
- [[PrintDisplay]] — 인쇄 레이아웃 변경

## Contradictions

- 없음
