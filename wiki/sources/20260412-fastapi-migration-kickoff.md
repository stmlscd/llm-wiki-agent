---
title: "FastAPI 마이그레이션 착수 — 계획 수립 + 인프라 준비 (2026-04-12)"
type: source
tags: [fastapi, migration, infrastructure, mac-mini, university-league]
date: 2026-04-12
source_file: raw/20260412_fastapi_migration_kickoff.md
---

## Summary

52회 회장기 대회 종료일에 Mac 현장 작업물을 Windows로 역동기화하고, FastAPI 기반 대학연맹 시스템(university-league)의 실행계획(Phase 0~9, 21일)을 수립했다. Mac mini 테스트 환경 구축, 외부 접속(www.sellma.kr) 확인, Alembic 초기 마이그레이션까지 완료하여 개발 착수 준비를 마쳤다.

## Key Claims

- Windows PC(F:\52nd_president_cup)가 메인 소스로 확정, Mac은 테스트/백업용
- Phase 0~9 총 21일 계획: 골격→인증→로직이��→리그→대진표→단체전→시상→신규기능→배포
- 검증된 알고리즘(tiebreak.py, draw.py, tournament.py)은 재작성 금지, I/O 레이어만 변경
- 모든 DB 테이블에 league_id FK 포함하여 멀티테넌시 준비 (elementary/university/ktta)
- main.js 3000줄 모노리스 → 6개 Alpine.js x-data 컴포넌트로 분리 (4/11 사고 재발 방지)
- Mac mini 테스트 서버: www.sellma.kr:80 / :8843 외부 접속 확인

## Connections

- [[UniversityLeague]] — 이 프로젝트의 실제 착수
- [[TechStackMigration]] — Flask→FastAPI 전환 계획의 실행 단계
- [[DeploymentArchitecture]] — Mac mini 테스트 환경 + www.sellma.kr 외부 접속 추가
- [[FieldOperationLessons]] — main.js 모노리스 사고 교훈이 Alpine.js 분리 결정의 직접 원인
- [[Sellma]] — sellma.kr 도메인으로 테스트 서버 연결

## Contradictions

- 없음. 기존 계획(TechStackMigration, UniversityLeague)의 구체적 실행으로, 방향성 변경 없음.
