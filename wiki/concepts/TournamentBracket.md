---
title: "TournamentBracket (본선 대진표 및 시드 배치)"
type: concept
tags: [algorithm, bracket, seeding, tournament, table-tennis]
sources: [52nd-president-cup]
last_updated: 2026-04-10
---

# TournamentBracket (본선 대진표 및 시드 배치)

[[52ndPresidentCup]] 본선에서 각 조 1·2위 선수를 대진표에 배치하고, 경기 결과를 기록하며 다음 라운드로 자동 진출시키는 시스템.

## 대진 규모 결정

```python
if 조_수 <= 8:
    bracket_size = 16  # 16강
else:
    bracket_size = 32  # 32강
```

## 시드 배치 알고리즘

### 슬롯 유형

| 슬롯 위치 | 유형 | 배경색 |
|-----------|------|--------|
| 1,4,5,8,9,12,13,16,17,20,21,24,25,28,29,32 | 1위 슬롯 | 노란색 |
| 2,3,6,7,10,11,14,15,18,19,22,23,26,27,30,31 | 2위 슬롯 | 초록색 |

### 32강 시드 → 대진번호 매핑 (일부)

```
시드 1  → 대진 1    시드 32 → 대진 2
시드 17 → 대진 3    시드 16 → 대진 4
시드 9  → 대진 5    시드 24 → 대진 6
...
```
(출처: `시드번호 순서.pdf` 공식 도표)

### BYE 처리

- 참가 인원이 브래킷 크기에 미달 시 BYE 할당
- 가장 높은 시드번호의 2위 슬롯부터 BYE 배정
- BYE는 자동 다음 라운드 진출, 기록지 출력 제외

## 경기 진행 흐름

```
32강 (16경기) → 16강 (8) → 8강 (4) → 준결승 (2) → 결승 (1)
16강 (8경기) → 8강 (4) → 준결승 (2) → 결승 (1)
```

### 승자 자동 진출 로직

```python
# 홀수 경기 → 다음 라운드 선수1 슬롯
# 짝수 경기 → 다음 라운드 선수2 슬롯
def _advance_winner(match_num, winner):
    next_match = (match_num + 1) // 2
    slot = 1 if match_num % 2 == 1 else 2
    # tournament_matches 테이블 업데이트
```

## 데이터베이스 스키마

```
draw_results:
  category, draw_number (1-32), seed, name, team,
  group_name, group_rank (1 or 2)

tournament_matches:
  category, round_name (32강/16강/8강/준결승/결승),
  match_number, draw1, draw2,
  player1_name, player1_team, player2_name, player2_team,
  player1_score, player2_score, game_scores (JSON),
  winner_draw, is_completed
```

## API

```
GET  /draw/<cat>              → 시드 배치 UI (드래그 앤 드롭)
POST /draw/<cat>/save         → 대진 저장 (관리자)
GET  /bracket/<cat>           → 대진표 시각화
GET  /bracket/<cat>/data      → 대진표 JSON API
GET  /match/<cat>/<round>/<num>   → 경기 결과 입력 화면
POST /match/<cat>/<round>/<num>   → 결과 기록
```

## 구현 파일

- `dist/draw.py` — 시드 배치 + 대진 생성
- `dist/tournament.py` — 경기 결과 기록 + 승자 진출
- `dist/routes_stage2.py` — Flask Blueprint 라우트
- `dist/static/js/gracket-lite.js` — 브래킷 시각화

## print_bracket.html 오른쪽 절반 인덱스 공식 (2026-04-11 수정)

브래킷 인쇄 페이지에서 오른쪽 절반의 매치 번호 계산:

```jinja2
{# 잘못된 공식 (구버전) — 존재하지 않는 매치 조회 #}
{% set rm = half // (2 ** r) + mi + 1 %}

{# 올바른 공식 — 오른쪽 절반 시작 인덱스 = half // 2^(r+1) #}
{% set rm = half // (2 ** (r + 1)) + mi + 1 %}
```

16인 브래킷(half=8) 기준:

| 라운드 | r | 올바른 오른쪽 인덱스 | 구 공식(오류) |
|--------|---|---------------------|---------------|
| 16강 | 0 | 5,6,7,8 | 9,10,11,12 |
| 8강 | 1 | 3,4 | 5,6 |
| 준결승 | 2 | 2 | 3 |

### 주의: Flask 템플릿 실사용 경로

Flask 앱(`app.py`)은 동일 디렉토리의 `templates/` 폴더를 사용.
프로젝트에 `templates/templates/`(구버전)와 `templates/`(실사용) 이중 경로 혼재 — 수정 시 반드시 `templates/` 직접 경로 파일을 수정할 것.

## Connections

- [[TieBreakRanking]] — 시드 배정 입력값 (조 순위)
- [[ScoreSheet]] — 경기 기록지 출력
- [[TeamMatch]] — 단체전에서 동일 구조 재사용
- [[52ndPresidentCup]] — 본선 방식
- [[52nd-president-cup]] — 구현 소스
- [[20260411-bracket-fix-ngtos]] — 인덱스 공식 버그 수정 기록
