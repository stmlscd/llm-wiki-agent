---
title: "20260412 회장기 랭킹 포인트 산출 및 DB 업데이트"
type: source
tags: [ranking, hoejanggi, mysql, points, excel]
date: 2026-04-12
source_file: raw/20260412_ranking_hoejanggi_update.md
---

## Summary

52회 회장기 대회 종료 후 VPS MySQL에 기록된 경기 결과를 기반으로 4·5·6학년 남녀 291명에게 회장기 랭킹 포인트를 산출하여 rk_* 테이블에 반영. 신규 선수 39명 등록, rk_players 342→381명. 엑셀 보고서 2개(남자/여자) 생성.

## Key Claims

- 회장기는 R1 등급 대회로, 학년별 차등 포인트 (6학년 우승 60점~4학년 64강 0점)
- 참가점수 10점은 전 선수에게 일괄 부여
- 예선 3위까지는 본선 미진출이어도 64강 점수 부여, 4위 이하는 참가점수(10점)만
- VPS MySQL kettf_admin 계정 접속 거부 → root 계정으로 우회 필요
- pymysql 직접 접속 불가 (3306 포트 미개방) → paramiko SSH 경유 필수

## Key Quotes

> "대회에 참가한 선수에 모두 참가점수 10점을 부여한다" — 회장기-랭킹포인트.xlsx

> "5, 6학년 회장기 대회의 예선전 3,4위의 점수를 차등 적용하여 64강의 점수를 부여한다" — 회장기-랭킹포인트.xlsx

## Connections

- [[PlayerRanking]] — rk_* 테이블 구조 및 포인트 계산 로직
- [[52ndPresidentCup]] — 대회 결과 데이터 원본
- [[DeploymentArchitecture]] — VPS SSH 경유 MySQL 접속 패턴
- [[TournamentBracket]] — 본선 라운드별 배치(placement) 추출 원본

## Contradictions

- `rk_players` 행 수: 기존 위키([[PlayerRanking]])에 343명으로 기재되었으나, 실제 VPS 조회 시 342명이었음. 회장기 반영 후 381명. → PlayerRanking 페이지 업데이트 필요.
