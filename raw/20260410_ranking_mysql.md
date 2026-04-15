# 52nd 회장기 랭킹 API MySQL 마이그레이션 (2026-04-10)

## 작업 개요

제52회 회장기 전국 초등학교 탁구대회 운영 시스템에서 랭킹 데이터가 SQLite와 MySQL에 분산되어
랭킹 API 전체가 동작 불가 상태였던 문제를 발견·수정한 작업.

## 배경 및 문제

VPS 환경에서 대회 데이터(tournament_matches, players 등)는 MySQL로 이미 마이그레이션되었으나,
랭킹 데이터만 별도로 `ranking.db`(SQLite) 파일을 사용하도록 남겨져 있었다.

MySQL 전환 시점에 `rk_players`, `rk_scores`, `rk_rankings`, `rk_tournaments`, `rk_point_rules` 테이블을
MySQL에 생성하고 데이터를 옮겼지만, `app.py`의 랭킹 API 코드는 여전히 SQLite `get_ranking_db()` 함수를
사용하고 있었다. VPS에는 `ranking_2025/ranking.db` 파일이 존재하지 않아
랭킹 관련 API 전체가 동작 불가 상태였다.

## 데이터 구조

### MySQL rk_* 테이블 (마이그레이션 후 실제 저장 위치)

| 테이블 | 행 수 | 비고 |
|--------|-------|------|
| `rk_players` | 343 | 선수 정보 (name, gender, grade, province, school) |
| `rk_tournaments` | 5 | 대회 정보 (회장기, 교보컵, 유승민배, 종별, 문광부) |
| `rk_scores` | 773 | 선수별 대회 점수 |
| `rk_rankings` | 343 | 계산된 순위 + 총점 (rank_num 컬럼 — MySQL 예약어 `rank` 회피) |
| `rk_point_rules` | - | 성적등급별 포인트 규칙 |

### SQLite ranking.db (구 구조, 로컬 개발용으로만 유지)

- `players`, `tournaments`, `scores`, `rankings`, `point_rules` 테이블 (동일 구조)
- `rankings.rank` 컬럼 사용 (SQLite는 예약어 충돌 없음)

## 수정 내용

### app.py 변경 (dist/app.py 및 VPS)

**제거된 코드:**
```python
import sqlite3
RANKING_DB_PATH = os.path.join(os.path.dirname(__file__), '..', 'ranking_2025', 'ranking.db')

def get_ranking_db():
  conn = sqlite3.connect(RANKING_DB_PATH)
  conn.row_factory = sqlite3.Row
  conn.execute('PRAGMA foreign_keys = ON')
  return conn
```

**변경된 함수 5개:**
- `ranking_tournaments()` — rk_tournaments 테이블 조회
- `ranking_list(gender, grade)` — rk_players + rk_scores JOIN으로 순위 계산
- `ranking_player(player_id)` — 선수별 대회별 점수 조회
- `generate_hoejanggi_scores()` — 회장기 대회 결과 → rk_scores + rk_rankings 갱신
- `update_ranking_player(player_id)` — 선수 학년/점수 수정 → rk_rankings 재계산
- `export_ranking_excel(gender, grade)` — rk_players + rk_scores → 엑셀 파일 생성

**SQLite → MySQL 문법 전환:**
| SQLite | MySQL |
|--------|-------|
| `?` 플레이스홀더 | `%s` 플레이스홀더 |
| `INSERT OR REPLACE INTO` | `INSERT INTO ... ON DUPLICATE KEY UPDATE` |
| `INSERT OR IGNORE INTO` | `INSERT IGNORE INTO` |
| `conn.execute(sql).fetchall()` | `cur = conn.cursor(); cur.execute(sql); cur.fetchall()` |
| 테이블 `players/scores/rankings` | `rk_players/rk_scores/rk_rankings` |
| 컬럼 `rank` | `rank_num` (MySQL 예약어 충돌 회피) |

## 환경 구조 (web/ vs dist/)

| 디렉토리 | DB 엔진 | ranking 위치 | 용도 |
|----------|---------|--------------|------|
| `web/` | SQLite | ranking_2025/ranking.db | 로컬 개발 |
| `dist/` | MySQL | rk_* 테이블 (MySQL) | VPS 배포 소스 |
| VPS | MySQL | rk_* 테이블 (MySQL) | 프로덕션 |

- `web/db.py` → SQLite, `dist/db.py` → MySQL (pymysql)
- `web/app.py`는 SQLite 기반 유지 (로컬 ranking.db 존재)
- `dist/app.py`와 VPS `app.py`는 동일 (MySQL 기반)

## 교훈

- DB 마이그레이션 시 일부 API만 부분 마이그레이션된 경우 조용한 장애 발생 가능
- MySQL 예약어(`rank`, `order`, `group` 등)는 컬럼명으로 사용 금지 → 접두사(`rank_num`) 사용
- 두 DB를 혼용하는 구조에서는 파일 경로 확인이 중요

## 배포

- VPS `app.py` 수정 후 `pkill -HUP gunicorn`으로 재로드
- `/api/ranking/tournaments`, `/api/ranking/남/6`, `/api/ranking/player/1` API 동작 확인
- dist/app.py → m4sellma 동기화 완료
