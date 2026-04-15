---
title: "20260414 단체전 세트 점수 오류 수정 및 검증 로직 추가"
type: source
tags: [team-match, validation, score, bug-fix, server]
date: 2026-04-14
source_file: talk/2026_04_14_talk.md
---

## Summary

단체전 32강 인천창신초 vs 제천홍광초 6번 경기(맹세주 vs 박해율)에서 3:0이어야 할 세트 스코어가 2:0으로 잘못 기록된 버그를 발견. 서버 DB 데이터를 수정하고, 프론트엔드+백엔드 양쪽에 탁구 세트 점수 유효성 검증 로직을 추가하여 재발을 방지했다.

## Key Claims

- 프론트엔드에서 세트 점수를 수집할 때 비어있는 세트를 건너뛰어 `player1_score`/`player2_score`가 실제보다 적게 계산되는 버그 존재
- 서버가 프론트엔드의 `player1_score`/`player2_score`를 검증 없이 그대로 저장하는 구조가 원인
- 수정: 서버에서 `set_scores` 배열로부터 직접 세트 승수를 재계산 (프론트엔드 값 불신뢰)
- 탁구 세트 점수 유효성 검증 규칙 추가: 11점제, 듀스(10:10 이후 2점차), 오타 방지(50점 초과 차단)
- VPS 서버(115.68.177.35) MySQL DB에서 match_id=78, game_order=6의 set_scores를 직접 수정
- `SessionStart` 훅을 설정하여 새 세션 시작 시 llm-wiki 우선 확인 규칙 자동 적용

## Key Quotes

> "3:0으로 기록이 되어야 하는데, 2:0으로 기록되었어. 이러한 일이 발생되면 안되는데."

> "7:111은 입력되면 안되는 것이고, 점수차이가 11점 이상 나면 탁구 경기가 아닌거야. 10:10인 경우를 듀스라고 하는데, 이러한 경우에도 오류를 알려줘야돼."

## Connections

- [[TeamMatch]] — 단체전 세트 점수 저장/조회 로직 핵심 수정
- [[FieldOperationLessons]] — 데이터 입력 오류 패턴(④)의 구체적 사례 및 해결
- [[DeploymentArchitecture]] — VPS MySQL + 로컬 SQLite 이중 환경 모두 수정 필요

## 수정 파일 목록

### 서버 백엔드 (set_scores 기반 재계산 + 유효성 검증)
- `web/routes_team.py` — `_validate_set_score()` 추가, `save_team_match_result()` 수정
- `web/routes_stage2.py` — 개인전 본선에도 검증 추가
- `web/app.py` — 예선리그에도 검증 추가
- `52nd_president_cup/web/server/routes_team.py` — MySQL 버전 동일 수정
- `52nd_president_cup/web/server/routes_stage2.py` — MySQL 버전 동일 수정

### 프론트엔드 (validateSetScore + 3선승 검증)
- `web/static/js/main.js` — 로컬
- `52nd_president_cup/web/static/js/main.js` — 서버 소스
- `52nd_president_cup/dist/static/js/main.js` — 배포

### 설정
- `.claude/settings.json` — SessionStart 훅 추가
- `CLAUDE.md` — 작업 시작 규칙 추가

## 검증 규칙 (validateSetScore)

| 입력 | 결과 |
|------|------|
| 11:0~11:9 | 정상 |
| 12:10, 13:11 | 정상 (듀스) |
| 7:111 | 차단 (오타) |
| 10:10 | 차단 (동점/듀스 미완료) |
| 11:10 | 차단 (불가능) |
| 15:11 | 차단 (듀스 2점차 아님) |
| 20:5 | 차단 (11점에서 끝남) |
