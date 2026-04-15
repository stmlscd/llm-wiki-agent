---
title: "KTTA 대회운영시스템 DB 구조 분석 (2026-04-11)"
type: source
tags: [database, KTTA, MariaDB, ERD, tournament-system]
ingested: "2026-04-11"
source_path: "docs/ngtos_db_schema.txt, docs/db_erd_data.json"
---

# KTTA 대회운영시스템 DB 구조 분석

## 배경

대한탁구협회(KTTA) 대회운영시스템(ngtos_tt)의 DB 구조를 분석하여, 우리 웹앱(52nd_president_cup)과의 데이터 연동 가능성을 파악.

## 시스템 환경

| 구분 | 서버 | 포트 | DB명 | 테이블 | MariaDB 버전 | 데이터 범위 |
|------|------|------|------|--------|-------------|------------|
| 로컬 구버전 | 127.0.0.1 | 3306 | gtos_tt | 158개 | MySQL 3.23 | ~2023 |
| 로컬 신버전 | 127.0.0.1 | 3307 | ngtos_tt | 188개 | MariaDB 10.8.3 | ~2023 |
| 원격 서버 | 182.162.101.245 | 13834 | ngtos_tt | 188개+ | MariaDB 11.4.2 | 현재 |

- 클라이언트: `C:\newgtos\ngtos_tt\ngtos_tt.exe` (PowerBuilder 기반)
- 설정: `C:\newgtos\ngtos_tt\tos.ini`
- KTTA VPN(PPTP, 220.117.160.242) 연결 필요
- 로컬 DB는 대회운영프로그램 설치 시 자동 설치됨

## 3개 대회 코드

| 대회코드 | 대회명 | 비고 |
|----------|--------|------|
| 202513537 | 제3회 유승민 전 IOC 위원배 U12 전국 챔피언 탁구대회 | 2025년 |
| 202513027 | 제41회 교보생명컵 꿈나무 탁구대회 | 2025년 |
| 202614448 | 제52회 회장기 전국 초등학교 탁구대회 | 2026년 |

3개 대회 데이터는 **원격 서버에만** 존재 (로컬은 2023년까지).

## DB 핵심 테이블 구조 (188개 중 주요 43개)

### 대회 관리
- **tour** — 대회 마스터 (PK: CLASS_CD, TO_CD) / 165건
- **tour_detail** — 대회 상세 설정 (라운드, 인원 등)
- **detail_class_cd** — 세부종목 정의 (PK: TO_CD, DETAIL_CLASS_CD) / 2,191건
  - NSL_GBN: 리그전 여부, NST_ROUND: 토너먼트 라운드 수
  - MA_TYPE: 경기 방식, SEX_CD: 성별

### 등록 관리
- **player** — 선수 마스터 (PK: PCLASS_CD, ID_NO) / 12,009건
- **tourplayer** — 대회 참가선수 (PK: CLASS_CD, TO_CD, ID_NO, KIND_CD) / 59,859건
- **tourplayerdetail** — 선수별 세부종목 배정 / 71,758건
- **tourteam** — 대회 참가팀 (PK: CLASS_CD, TO_CD, entrant_team_cd) / 14,036건
- **teaminfo** — 팀 마스터 (PK: PCLASS_CD, REG_YEAR, TEAM_CD) / 5,189건
- **seasonplayer** — 시즌 선수 등록 / 26,453건

### 리그전 (nsl = National Sports League)
- **nslgroup** — 리그 그룹(조) 구성 / 1,161건
- **nsldaejin** — 리그 대진 배정 / 4,563건
- **nslresult** — 리그 경기결과 (WINNER_GB, SCORE_L/R) / 10,172건
- **nslresultdetail** — 세트별 스코어 (PLAY_SEQ, SEQ별) / 139,670건
- **nslschedule** — 경기 일정 / 10,511건
- **nslresult_sum** — 리그 순위 합산 / 3,168건
- **nslresult_sub** — 리그 부가결과 / 32,598건

### 토너먼트 (nst = National Sports Tournament, nsd = 대진)
- **nsddaejin** — 토너먼트 대진 (POSITION, SEED_NO) / 45,834건
- **nstresult** — 토너먼트 경기결과 (RH_CD, WINNER_GB) / 58,783건
- **nstresultdetail** — 토너먼트 세트별 스코어 / 120,060건
- **matchschedule** — 경기 일정 (VENUE_CD, COURT_NO) / 59,115건
- **nsdrhinfo** — 라운드 헤더 정보 / 502건

### 실적/시상
- **playerresult** — 선수별 대회 실적 (RANKING, RECORD_NM) / 30,766건
- **teamresult** — 팀 대회 실적 / 19건
- **tourprise** — 시상 내역 / 37건

### 코드 테이블
- **sido_cd** — 시도 코드 (34건)
- **co_cd** — 국가 코드 (252건)
- **kind_cd** — 종별 코드 (32건)
- **venue_cd** — 경기장 코드 (184건)
- **reason_cd** — 사유 코드 (13건, 기권/실격 등)
- **pclass_cd** — 종목 분류 코드 (55건)
- **class_cd** — 클래스 코드 (탁구=09)

## 핵심 관계 (PK-FK)

```
tour (CLASS_CD, TO_CD)
  ├→ detail_class_cd (TO_CD, DETAIL_CLASS_CD)
  │    ├→ nslgroup (리그전 그룹)
  │    │    ├→ nsldaejin (리그 대진)
  │    │    ├→ nslresult (리그 결과)
  │    │    │    └→ nslresultdetail (세트별 점수)
  │    │    └→ nslschedule (리그 일정)
  │    ├→ nsddaejin (토너먼트 대진)
  │    │    └→ nstresult (토너먼트 결과)
  │    │         └→ nstresultdetail (세트별 점수)
  │    └→ matchschedule (경기 일정)
  ├→ tourplayer (참가선수)
  │    └→ tourplayerdetail (세부종목 배정)
  └→ tourteam (참가팀)

player (PCLASS_CD, ID_NO)
  ├→ tourplayer (대회 참가)
  └→ playerresult (실적)
```

## 우리 웹앱과의 매핑

| KTTA 테이블 | 우리 웹앱 | 비고 |
|------------|----------|------|
| tour | tournament.db → tournaments | 대회 정보 |
| detail_class_cd | categories | 세부종목 |
| tourplayer | players | 참가선수 |
| tourteam | teams | 참가팀 |
| nslgroup | groups | 리그 그룹 |
| nslresult + nslresultdetail | matches + match_sets | 리그 경기/스코어 |
| nsddaejin | bracket_entries | 토너먼트 대진 |
| nstresult + nstresultdetail | bracket_matches + sets | 토너먼트 경기 |

## 산출물
- `docs/ngtos_db_schema.txt` — 전체 테이블 스키마 덤프
- `docs/db_erd_data.json` — ERD용 테이블/관계 JSON
- `docs/db_schema_dump.txt` — gtos_tt(구버전) 스키마
- ERD 다이어그램 (Excalidraw)
