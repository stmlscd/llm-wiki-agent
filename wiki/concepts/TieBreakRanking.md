---
title: "TieBreakRanking (공방율 동률 처리)"
type: concept
tags: [algorithm, tie-break, ranking, ittf, table-tennis]
sources: [52nd-president-cup]
last_updated: 2026-04-10
---

# TieBreakRanking (공방율 동률 처리)

[[52ndPresidentCup]] [[RoundRobinLeague]] 완료 후 동 순위를 결정하는 알고리즘. ITTF 규정(`부록1 리그전시 순위 계산.pdf`)을 기반으로 구현.

## 우선순위 (3단계)

1. **승수(Wins)** — 승리 횟수 내림차순
2. **공방율(Game Ratio)** — 게임 승수 / 게임 패수, 내림차순
3. **맞대결 공방율(Head-to-Head Ratio)** — 동률 선수 간 경기만 재계산

## 공방율 계산

```python
# 게임 수 기준 (세트 점수 합계가 아님)
ratio = games_won / games_lost

# 전승(게임 패 없음) 처리
if games_lost == 0:
    ratio = 999.999  # ∞ 처리
```

**예시**: 3-1로 승리 → games_won += 3, games_lost += 1

## 데이터베이스 스키마

```
tiebreak_results:
  category, group_name, rank,
  seed, name, team,
  wins, losses, points_for, points_against, ratio
```

## API

```
GET  /rankings           → 부문 선택 화면
GET  /rankings/<cat>     → 해당 부문 순위 결과
POST /rankings/<cat>/calculate  → 동률 계산 실행 (관리자)
```

## 구현 파일

- `dist/tiebreak.py` — 핵심 알고리즘
- `dist/routes_stage2.py` — Flask Blueprint 라우트

## 주의사항

- 게임 점수 합계가 아닌 **게임 수(세트 수)**를 사용
- 전패(게임 승 없음)일 경우: ratio = 0
- 맞대결은 동률 선수들끼리만 재계산 (전체 리그 결과 사용 안 함)

## Connections

- [[RoundRobinLeague]] — 계산 대상 리그
- [[TournamentBracket]] — 순위 결과를 시드 배정에 사용
- [[KETTF]] — ITTF 규칙 기반 채택
- [[52nd-president-cup]] — 구현 소스
