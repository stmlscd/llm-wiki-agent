---
title: "20260411 브래킷 버그 수정 & ngtos_tt DB 분석"
type: source
tags: [tournament-bracket, bug-fix, flask-template, ngtos, ktta, sync]
date: 2026-04-11
source_file: raw/20260411_bracket_fix_and_ngtos.md
last_updated: 2026-04-11
---

## Summary

2026-04-11 대회 현장에서 Windows PC(현장 컴)와 MacBook Air 양방향으로 작업. 핵심은 `print_bracket.html` 오른쪽 절반 결과 미표시 버그 수정. ngtos_tt(KTTA 공식 DB) 구조 분석으로 대학연맹 FastAPI 시스템 설계 사전조사 병행.

## Key Claims

- **브래킷 인덱스 공식 버그**: 오른쪽 절반 매치 번호 계산에서 `half // (2 ** r)` → `half // (2 ** (r + 1))` 수정. 16인 브래킷에서 구공식은 존재하지 않는 매치 9~12를 조회함.
- **Flask 템플릿 경로 혼동**: `dist/templates/templates/`(구버전 미사용)과 `dist/templates/`(실사용) 이중 경로 혼재. Flask는 `app.py` 위치의 `templates/`를 사용.
- **VPS 별도 배포 필요**: m4sellma rsync는 백업용이며 VPS(`/var/www/kettf/2026/52nd/`)에는 scp로 별도 배포해야 반영됨.
- **Windows 동기화 덮어쓰기 위험**: 수정 후 Windows 동기화 실행 시 구버전이 다시 덮어씌워짐 → 동기화 후 재수정 필수.
- **ngtos_tt**: 188개 테이블, 7개 도메인, 43개 핵심 테이블 추출. 대학연맹 시스템 설계 참조용.

## Key Quotes

> "dist/templates/templates/ 안의 것들 — 실제로 사용되지 않는 파일들이었습니다."

> "윈도우 동기화로 2개 파일이 구버전으로 덮어씌워졌습니다."

## Connections

- [[TournamentBracket]] — 브래킷 인덱스 계산 공식 버그 수정, 오른쪽 절반 매치 조회 로직
- [[DeploymentArchitecture]] — Flask 템플릿 경로 구조, VPS 별도 배포 필요성 확인
- [[PrintDisplay]] — print_bracket.html 오른쪽 결과 표시 수정
- [[UniversityLeague]] — ngtos_tt DB 분석은 대학연맹 시스템 설계 사전조사

## Contradictions

- 없음. 기존 DeploymentArchitecture가 m4sellma→VPS 자동 배포를 전제했다면 수정 필요 — VPS 배포는 수동 scp임.
