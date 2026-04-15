---
title: "2026-04-10 전략 분석 및 다음 단계 계획"
type: source
tags: [strategy, fastapi, refactoring, lessons-learned, university-league]
date: 2026-04-10
source_file: raw/20260410_strategy_and_next_phase.md
---

## Summary

52회 회장기 3일간(4/8~4/10) 현장 운영 중 발생한 30개 이상의 수정·배포 작업을 분석하여 6가지 반복 패턴을 도출하였다. 분석을 바탕으로 기술 스택을 Flask → FastAPI, Vanilla JS → Alpine.js로 점진적 전환하기로 결정하였으며, 52회 완주 후 대학연맹 시스템을 FastAPI로 새로 시작한다.

## Key Claims

- 현장 트러블의 대부분은 기술 선택 문제가 아닌 **구조 문제** (web/dist 이중 코드베이스, 캐시 수동 관리, DB 환경 혼선)에서 발생함
- 브라우저 캐시 문제가 4회 반복됨 — 캐시 버스팅 자동화 필요
- 단체전 게임 순서 오입력(4↔5 스왑)은 시스템의 입력 검증 부재로 발생한 중대 데이터 오류
- 한국 최대 PHP BBS인 그누보드가 FastAPI로 전환(그누보드6) — FastAPI 방향의 외부 검증
- 대학연맹 프로젝트는 처음부터 FastAPI + Alembic + Pydantic + Alpine.js로 설계
- 52nd의 핵심 알고리즘(tiebreak, draw)과 렌더러(gracket-lite)는 이식 자산으로 보존

## Key Quotes

> "현재 대부분의 고통은 기술 자체가 아닌 구조 문제에서 온다." — 분석 보고서

> "변경하면 안 되는 것: tiebreak.py, draw.py, gracket-lite.js, 인쇄 CSS" — 기술 검토

> "52회 완주 후 대학연맹 프로젝트를 통해 FastAPI로 다른 방식의 시스템을 만들어야 한다." — 사용자

## Connections

- [[52ndPresidentCup]] — 3일간 분석의 대상 프로젝트
- [[DeploymentArchitecture]] — web/dist 이중 구조 문제의 근원
- [[TechStackMigration]] — Flask → FastAPI 전환 결정 (신규 개념)
- [[UniversityLeague]] — 다음 프로젝트 (신규 엔티티)
- [[Gnuboard6]] — FastAPI 방향 검증 (신규 엔티티)
- [[TeamMatch]] — 게임 순서 오입력 문제와 연관
- [[PlayerRanking]] — MySQL 마이그레이션 완료 상태

## Contradictions

- 없음. 기존 위키의 "미해결/확장 예정" 항목들이 이 소스에서 구체적 계획으로 전환됨.
