---
title: "RoundRobinLeague (조별 리그전)"
type: concept
tags: [tournament, round-robin, algorithm, table-tennis]
sources: [52nd-president-cup]
last_updated: 2026-04-10
---

# RoundRobinLeague (조별 리그전)

[[52ndPresidentCup]] 예선에서 사용하는 조별 리그 방식. 같은 조 내 모든 선수가 서로 한 번씩 경기하며 순위를 결정한다.

## 경기 순서 (고정 스케줄)

조 인원수별 고정된 대진 순서를 사용한다:

```python
MATCH_ORDERS = {
  2: [[1,2]],
  3: [[2,3],[1,3],[1,2]],
  4: [[1,4],[2,3],[1,3],[4,2],[1,2],[3,4]],
  5: [[2,5],[3,4],[1,5],[2,3],[1,4],[5,3],[1,3],[4,2],[1,2],[4,5]]
}
```

## 점수 체계

| 결과 | 점수 |
|------|------|
| 승리 | 2점 |
| 패배 | 1점 |
| 몰수패 | 0점 |

## 경기 결과 저장 형식

게임 스코어는 JSON 배열로 저장:

```json
[{"s1": 11, "s2": 9}, {"s1": 9, "s2": 11}, {"s1": 11, "s2": 7}]
```

## 데이터베이스 스키마

```
results:
  match_num, seed1, seed2,
  game_scores (JSON),
  winner_seed, points_for, points_against,
  forfeit_flag
```

## UI 기능

- 조별 리그표 실시간 조회 (`/api/players/<cat>/<grp>`)
- 경기 결과 입력 (`POST /api/result/<cat>/<grp>/<match_num>`)
- 기록지 인쇄 (`/print/scoresheet/<cat>/<grp>/<match_num>`)
- 앞장(리그표) 인쇄 (`/print/league/<cat>/<grp>`)

## 몰수 처리

- 불참 선수 → 관리자가 `/api/forfeit`로 등록
- 해당 선수의 모든 미완료 경기 자동 몰수 처리
- 그룹 리그 재계산

## Connections

- [[TieBreakRanking]] — 리그 완료 후 동률 처리
- [[ScoreSheet]] — 경기 기록지 출력
- [[52ndPresidentCup]] — 예선 방식으로 채택
- [[52nd-president-cup]] — 구현 소스
