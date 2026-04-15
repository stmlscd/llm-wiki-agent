---
title: "랭킹 API MySQL 마이그레이션 (2026-04-10)"
type: source
tags: [ranking, mysql, sqlite, migration, flask, python]
date: 2026-04-10
source_file: raw/20260410_ranking_mysql.md
---

## Summary

VPS 환경에서 대회 데이터는 MySQL로 마이그레이션되었으나 랭킹 API 코드만 SQLite `ranking.db`를 바라보도록 남겨져 전체 랭킹 API가 동작 불가 상태였던 문제를 발견·수정한 작업 기록. `app.py`에서 `get_ranking_db()` / SQLite 의존성을 제거하고 6개 랭킹 함수를 `db.get_conn()` + MySQL 문법으로 전환했다. MySQL 예약어 `rank` 충돌로 `rk_rankings` 테이블의 컬럼명은 `rank_num`을 사용한다.

## Key Claims

- VPS에 `rk_players`(343명), `rk_scores`(773건), `rk_rankings`(343건), `rk_tournaments`(5개), `rk_point_rules` 테이블이 존재했으나 앱 코드는 존재하지 않는 `ranking_2025/ranking.db` SQLite 파일을 참조하고 있었다.
- MySQL에서 `rank`는 예약어이므로 `rk_rankings` 테이블 순위 컬럼명은 `rank_num`으로 정의해야 한다.
- SQLite `?` 플레이스홀더 → MySQL `%s`, `INSERT OR REPLACE` → `ON DUPLICATE KEY UPDATE`, `INSERT OR IGNORE` → `INSERT IGNORE` 변환 필요.
- pymysql은 커서 기반 API: `cur = conn.cursor(); cur.execute(sql); cur.fetchall()` — SQLite의 `conn.execute().fetchall()` 패턴 불가.
- `web/` 디렉토리(로컬 개발)는 SQLite 기반을 유지하고 `dist/` + VPS만 MySQL로 운영하는 이중 구조를 의도적으로 유지.

## Key Quotes

> "DB 마이그레이션 시 일부 API만 부분 마이그레이션된 경우 조용한 장애 발생 가능"

> "MySQL 예약어(`rank`, `order`, `group` 등)는 컬럼명으로 사용 금지 → 접두사(`rank_num`) 사용"

## Connections

- [[DeploymentArchitecture]] — web/SQLite vs dist+VPS/MySQL 이중 구조의 구체적 사례
- [[PlayerRanking]] — 랭킹 시스템의 MySQL 구현 세부사항
- [[52ndPresidentCup]] — 랭킹 데이터는 이 대회 시스템의 3단계(전국 랭킹 집계)에 해당

## Contradictions

- 없음. overview.md의 "랭킹 시스템 보강 (미해결)" 항목이 이번 마이그레이션으로 부분 해소됨.
