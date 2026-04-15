---
title: "20260414 대통령기 점수 추가 및 랭킹 UI 업데이트"
type: source
tags: [ranking, president-cup, tournament-add, ui-fix]
date: 2026-04-14
source_file: raw/20260414_ranking_president_cup_add.md
---

## Summary

대통령기 대회(R2)를 rk_tournaments에 추가하고 5·6학년 남녀 147명 점수 입력. VPS 웹 UI(index.html + main.js)에 대통령기 컬럼 추가. 여자6학년 학교명 3건 수정. 엑셀 보고서 6개 대회 컬럼으로 재생성.

## Key Claims

- main.js의 codes 배열은 rk_tournaments를 동적으로 읽지 않고 하드코딩 — 대회 추가 시 반드시 수동 수정 필요
- 대통령기는 R2 등급, 종별과 문체부 사이 순서
- 4학년은 대통령기 점수 없음 (5·6학년만)

## Connections

- [[PlayerRanking]] — rk_tournaments 6개 대회 확정, codes 배열 하드코딩 주의
- [[DeploymentArchitecture]] — VPS sed 직접 편집으로 로컬/VPS 파일 차이 발생

## Contradictions

- 없음.
