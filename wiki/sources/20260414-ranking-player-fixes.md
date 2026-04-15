---
title: "20260414 랭킹 선수 정보 수정 및 중복 합산"
type: source
tags: [ranking, player-fix, merge, duplicate]
date: 2026-04-14
source_file: raw/20260414_ranking_player_fixes.md
---

## Summary

rk_players 학교명 수정 4건, 중복 선수 합산 2건(이율 공백 중복, 황찬율 학년 간 중복). 선수 수 381→380명. 남자 4·5학년 순위 전체 재계산. 엑셀 보고서 재생성.

## Key Claims

- "이 율"(공백 포함) 문제: 회장기 포인트 산출 시 tournament.db 선수명 공백이 원인 — rk_players에 별도 등록됨
- 황찬율: 종별선수권 시점에 4학년 등록, 이후 5학년으로 진급하여 다른 대회 참가 — 학년 간 이중 등록
- 학교명 오류(용황/장원→포은, 세아아카데미→장량): 대회별로 다른 소속으로 등록된 이력 데이터

## Connections

- [[PlayerRanking]] — rk_players/rk_scores/rk_rankings 수정
- [[52ndPresidentCup]] — 회장기 포인트 산출 시 발생한 공백 중복 원인

## Contradictions

- 없음.
