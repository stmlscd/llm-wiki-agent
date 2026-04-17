---
title: "20260417 KTTA 경기결과 자동 전송 계획"
type: source
tags: [ktta, automation, database, migration, plan]
date: 2026-04-17
source_file: raw/20260417_ktta_auto_transfer_plan.md
---

## Summary

tt-result → KTTA 대회운영 프로그램 로컬 DB(ngtos_tt, 127.0.0.1:3307, root/password) 삽입 방식으로 경기결과 전송 자동화. 4단계 계획: 매핑 스크립트 → 로컬 삽입 테스트 → 전송 테스트 → 대학연맹 적용.

## Key Claims

- 로컬 MariaDB 접속 확인: root/password (skip-grant-tables 없이도 가능)
- 원격 DB(182.162.101.245:13834): VPN으로 포트 OPEN이지만 계정 미확인
- game_scores JSON을 nslresultdetail/nstresultdetail 행으로 변환하는 것이 핵심
- 이전 3가지 실패(UI자동화, DB직접전송, 프로그램매칭)를 회피하는 전략
- DETAIL_CLASS_CD, 선수 ID_NO, KIND_CD 매핑이 선행 확인 필요

## Connections

- [[KTTA-TOS]] — 대회운영시스템 구조, DB 스키마 188개 테이블
- [[UniversityLeague]] — 대학연맹 4/28 대회에 첫 적용 예정

## Contradictions

- 없음
