---
title: "20260413 랭킹 시도 수정 및 버튼 삭제, 최종 백업"
type: source
tags: [ranking, province, cleanup, backup, final]
date: 2026-04-13
source_file: raw/20260413_ranking_province_fix_final.md
---

## Summary

rk_players의 시도(province) 데이터 45건 수정 (빈값/기타/오류 → 정확한 시도). 랭킹 메뉴에서 '회장기 점수 산출'/'엑셀 내보내기' 버튼 삭제. 엑셀 보고서 재생성. VPS 소스+DB '최종완성' 백업 후 m4sellma 동기화.

## Key Claims

- 시도 수정 45건: 빈값 → 정확한 시도, '기타' → 정확한 시도, 잘못된 시도 수정 (김해내동 경북→경남, 보람 충남→세종, 정산 충북→충남)
- 수정 후 빈값/기타 0건, 17개 시도 전부 정상
- 랭킹 메뉴 rk-action-btns div 전체 삭제 (VPS templates/index.html sed 직접 편집)
- 최종 백업: app_20260413_최종완성.tar.gz (7.1MB) + kettf_tournament_20260413_최종완성.sql (322KB)

## Key Quotes

> "지역표시.txt" — 학교명별 시도 매핑 원본 자료 (사용자 제공)

## Connections

- [[PlayerRanking]] — rk_players province 데이터 정리
- [[DeploymentArchitecture]] — VPS 최종 백업, m4sellma 동기화
- [[52ndPresidentCup]] — 대회 시스템 최종 마무리

## Contradictions

- 없음.
