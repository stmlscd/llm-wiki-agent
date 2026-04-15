---
title: "PlayerRanking (선수 랭킹 시스템)"
type: concept
tags: [ranking, mysql, points, annual]
sources: [52nd-president-cup, 20260410-ranking-mysql, 20260412-ranking-hoejanggi-update, 20260413-ranking-province-fix-final, 20260414-ranking-player-fixes]
last_updated: 2026-04-14
---

# PlayerRanking (선수 랭킹 시스템)

전국 초등학교 탁구대회에서 성별·학년별 연간 포인트를 집계하는 랭킹 시스템.

## 데이터 구조 (MySQL rk_* 테이블)

| 테이블 | 행 수 | 설명 |
|--------|-------|------|
| `rk_players` | 380 | 선수 기본 정보 (name, gender, grade, province, school) |
| `rk_tournaments` | 6 | 대회 정보 (회장기, 교보컵, 유승민배, 종별, 대통령기, 문광부) |
| `rk_scores` | 1064 | 선수별 대회 포인트 |
| `rk_rankings` | 380 | 계산된 순위 + 총점 (`rank_num` 컬럼) |
| `rk_point_rules` | 42 | 성적등급별 포인트 규칙 (rating_class = R1/R2) |

## 주요 API 함수

- `ranking_tournaments()` — 대회 목록 조회
- `ranking_list(gender, grade)` — 성별·학년별 순위표 (rk_players + rk_scores JOIN)
- `ranking_player(player_id)` — 선수별 대회 점수 상세
- `generate_hoejanggi_scores()` — 회장기 결과 → rk_scores + rk_rankings 갱신
- `update_ranking_player(player_id)` — 학년/점수 수정 후 rk_rankings 재계산
- `export_ranking_excel(gender, grade)` — 엑셀 내보내기

## 구현 주의사항

- `rank`는 MySQL 예약어 → `rk_rankings.rank_num` 사용
- `rk_` 접두사는 tournament_matches 등 일반 대회 테이블과 충돌 방지
- 환경별 DB: `web/` = SQLite (ranking_2025/ranking.db), `dist/` + VPS = MySQL
- pymysql 커서 패턴 필수: `cur = conn.cursor(); cur.execute(sql); cur.fetchall()`

## 회장기 포인트 산출 (2026-04-12 완료)

4·5·6학년 남녀 291명에게 회장기 포인트 부여. 신규 선수 39명 추가 등록.

### R1 포인트 테이블 (회장기)

| 배치 | 6학년 | 5학년 | 4학년 |
|------|-------|-------|-------|
| 우승 | 60 | 50 | 40 |
| 준우승 | 50 | 40 | 30 |
| 4강 | 40 | 30 | 20 |
| 8강 | 30 | 20 | 15 |
| 16강 | 20 | 15 | 10 |
| 32강 | 15 | 10 | 5 |
| 64강 | 10 | 5 | 0 |

- **참가점수**: 전원 10점
- **예선 3위까지**: 본선 미진출이어도 64강 점수 부여
- **예선 4위 이하**: 참가점수(10점)만

### 배치 판정 로직

`tournament_matches` 라운드별 패자 역추적: 결승 winner→우승 / loser→준우승 / 준결승 loser→4강 / 8강 loser→8강 / 16강 loser→16강 / 32강 loser→32강. 예선 `tiebreak_results` rank ≤ 3 + 본선 미진출 → 64강. 나머지 → 참가.

### 시도(province) 데이터 정리 (2026-04-13 완료)

45건 수정 — 빈값/기타/오류를 정확한 시도로 교정. 수정 후 17개 시도 전부 정상, 빈값·기타 0건.
주요 오류: 김해내동(경북→경남), 보람(충남→세종), 정산·청양정산(충북→충남).
매핑 원본: 사용자 제공 `지역표시.txt` (학교명→시도).

### UI 정리 (2026-04-13)

랭킹 메뉴에서 '회장기 점수 산출'·'엑셀 내보내기' 버튼 삭제 (rk-action-btns div 제거). VPS templates/index.html sed 직접 편집.

### 선수 정보 수정 및 중복 합산 (2026-04-14)

학교명 수정 4건(박해온·이하니·김나경→포은, 오리온→장량). 중복 합산 2건:
- **이율**(남5 오정): "이율"+"이 율"(공백) 별도 등록 → 합산 75점. 원인: tournament.db 선수명 공백.
- **황찬율**(남5 장량): 4학년 종별10점 + 5학년 55점 → 5학년 합산 65점. 원인: 학년 진급 시 이중 등록.

선수 수 381→380명 확정.

### 대통령기 추가 (2026-04-14)

대통령기 대회(R2) rk_tournaments id=6으로 추가. 5·6학년 남녀 147명 점수 입력 (4학년 없음). 대회 6개 체제 확정: 회장기(R1), 교보(R1), 유승민(R1), 종별(R2), 대통령기(R2), 문체부(R2). VPS UI(index.html + main.js) 수동 수정 — **main.js codes 배열 하드코딩 주의**.

### 접속 이슈

- `kettf_admin` 계정: VPS MySQL Access denied (2026-04-12 확인). root 계정 사용.
- MySQL 3306 외부 미개방 → paramiko SSH 경유 필수.

## 연결

- [[DeploymentArchitecture]] — 환경별 DB 분리 구조
- [[52ndPresidentCup]] — 이 랭킹 시스템이 속한 대회 운영 시스템
- [[TournamentBracket]] — 본선 배치 추출 원본
