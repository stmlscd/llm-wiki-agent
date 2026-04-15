---
title: "KTTADatabaseSchema (KTTA 대회운영시스템 DB)"
type: concept
tags: [database, MariaDB, KTTA, ngtos_tt]
sources: [sources/20260411-ktta-db-analysis.md]
last_updated: "2026-04-11"
---

# KTTA 대회운영시스템 DB (ngtos_tt)

대한탁구협회가 운영하는 공식 대회운영시스템의 데이터베이스. PowerBuilder 기반 Windows 클라이언트(ngtos_tt.exe)가 MariaDB에 직접 접속하여 사용.

## 구조 요약

- **188개 테이블**, MariaDB 10.8~11.4
- VPN(PPTP) 접속 필수 (원격: 182.162.101.245:13834)
- 종목코드: TT (탁구=09), CLASS_CD=09

## 6대 영역

1. **코드 마스터** — sido_cd, co_cd, kind_cd, venue_cd, reason_cd, pclass_cd, class_cd
2. **대회 관리** — tour, detail_class_cd, tour_detail (TO_CD가 핵심 FK)
3. **등록 관리** — player, tourplayer, tourplayerdetail, tourteam, teaminfo
4. **리그전** — nslgroup → nsldaejin → nslresult → nslresultdetail (nsl 접두사)
5. **토너먼트** — nsddaejin → nstresult → nstresultdetail (nsd/nst 접두사)
6. **실적/시상** — playerresult, teamresult, tourprise

## 네이밍 규칙
- `nsl` = National Sports League (리그전)
- `nst` = National Sports Tournament (토너먼트)
- `nsd` = 대진 관련
- `nsr` = 기록 관련
- `link_` = 외부 연동 테이블
- `pf_` = 플랫폼/권한 관리

## 우리 시스템과의 관계

우리 웹앱(52nd_president_cup)은 이 시스템과 **병행 운영**됨:
- KTTA 시스템: 공식 결과 입력/보고
- 우리 시스템: 현장 실시간 조회, 인쇄, 랭킹 관리

향후 데이터 연동(MySQL → 우리 DB 동기화) 가능성 있음.
