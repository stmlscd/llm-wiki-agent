---
title: "20260414 탁구경기결과관리시스템(tt-result) 디자인 문서 확정"
type: source
tags: [tt-result, design, fastapi, university-league, planning]
date: 2026-04-14
source_file: ~/.gstack/projects/52nd_president_cup/amd.sellma01-unknown-design-20260414-210626.md
---

## Summary

gstack /office-hours를 통해 탁구경기결과관리시스템(tt-result) 디자인 문서를 작성하고 승인했다. 여러 연맹(초등/대학/중고/실업)의 대회를 하나의 FastAPI 코드베이스로 관리하는 통합 시스템. Approach C(단계적 릴리스) 선택 — 모듈러 아키텍처로 대학연맹 최소 기능 먼저 완성, 현장 피드백으로 보강.

## Key Claims

- **첫 실전**: 대학연맹 대회 (2026-04-28 ~ 05-02). D-14가 2026-04-14(오늘)
- **기술 스택**: FastAPI + Alpine.js + Jinja2 + MySQL + Alembic
- **핵심 설계 원칙**: 기능별 모듈 분리 (league.js/bracket.js/team.js 독립). main.js 모노리스 연쇄 사고 재발 방지
- **MySQL 단일 환경** (SQLite 제거 확정). DB 연맹별 분리: kettf_db(초등), kuttf_db(대학), ktta_db(협회), tt_result(통합 메타)
- **경기 방식 프리셋**: Enum 배열로 정의 (STANDARD_7, FOUR_ONE, FIVE_SINGLES, FIVE_TWO)
- **Pydantic 세트 점수 검증**: 11점제/듀스 규칙을 모델 레벨에서 강제
- **Phase 1** (1.5주, ~4/28): 선수 등록 + 예선 리그 + 본선 대진표 + 경기 결과 입력 + 단체전
- **Phase 2** (대회 후 3주): 랭킹 통합, 시상, 코치/팀 로그인, 선수 CRUD
- **Phase 3**: 중고/실업 연맹 확장, KTTA 연동

## Key Quotes

> "MySQL 단일 환경으로 운용하는 것을 원칙으로 한다."

> "대부분의 고통은 기술 선택이 아닌 구조 문제에서 발생" (FieldOperationLessons)

## Connections

- [[UniversityLeague]] — 첫 실전 대상, university-league/ 디렉토리 기존 골격 활용
- [[TechStackMigration]] — FastAPI+Alpine.js 전환 결정의 실제 적용
- [[TeamMatch]] — 경기 방식 프리셋(Enum) 설계
- [[FieldOperationLessons]] — 모노리스 연쇄 사고, 데이터 입력 오류 등 현장 교훈 반영
- [[DeploymentArchitecture]] — MySQL 단일 환경, VPS 배포

## 디자인 문서 파일

`~/.gstack/projects/52nd_president_cup/amd.sellma01-unknown-design-20260414-210626.md`

## 리뷰 결과

- 독립 리뷰어 1라운드: 13개 이슈 발견, 11개 수정
- 주요 수정: 인증 방식(관리자 PIN), DB 이름 매핑, 경기방식 프리셋 배열, 결과 수정 API, tiebreak 알고리즘, 오프라인 폴백
