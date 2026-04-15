---
title: "TechStackMigration (기술 스택 전환 계획)"
type: concept
tags: [fastapi, alpine-js, flask, migration, architecture]
sources: [20260410-strategy-next-phase]
last_updated: 2026-04-10
---

# TechStackMigration (기술 스택 전환 계획)

52nd 현장 분석을 바탕으로 결정된 점진적 기술 전환 계획.

## 전환 방향

| 현재 | 전환 목표 | 이유 |
|------|-----------|------|
| Flask | FastAPI | Pydantic 검증, Alembic 마이그레이션, 자동 API 문서 |
| Vanilla JS (3000줄 모노리스) | Alpine.js | 빌드 없음, 선언적 반응형, 전역변수 제거 |
| 수동 SQL 마이그레이션 | Alembic | 재현 가능한 DB 변경 이력 |
| web/ + dist/ 이중 구조 | 단일 코드베이스 | 환경변수(USE_MYSQL)로 DB 전환 |
| 수동 캐시버스팅 | 파일 해시 자동 주입 | 배포 후 수동 편집 제거 |

## 유지할 자산 (교체 금지)

- **`tiebreak.py`** — ITTF 규정 기반 동률 처리, 현장 검증 완료
- **`draw.py`** — 시드 배치 로직, 공식 배치도 기반
- **`gracket-lite.js`** — jQuery 없는 경량 대진표 렌더러
- **인쇄 CSS (A4 최적화)** — 수많은 시행착오의 결과물
- **SQLite 로컬 지원** — 오프라인 대회장 필수

## Alpine.js 선택 이유

React/Vue 대비 Alpine.js의 핵심 장점:
- **빌드 단계 없음** — CDN 한 줄로 도입, 오프라인 환경에서 파일만 있으면 즉석 수정 가능
- Jinja2 템플릿과 완전 호환 — 서버 렌더링 + 클라이언트 반응형 혼합 가능
- `x-data`, `x-for`, `x-show` 등 직관적 선언적 문법

## FastAPI 도입 시 해결되는 문제

| 문제 (52nd 현장) | FastAPI 해결책 |
|------------------|---------------|
| 게임 순서 오입력 → DB 수동 스왑 | Pydantic Enum으로 허용값 외 입력 차단 |
| reason 컬럼 추가 시 수동 ALTER TABLE | Alembic migration으로 재현 가능한 변경 |
| API 동작 현장 확인 어려움 | /docs 자동 문서화 |
| SQLite↔MySQL 코드 분기 | SQLAlchemy ORM으로 DB 추상화 |

## 참고: 그누보드6 검증

한국 최대 PHP BBS 그누보드가 FastAPI + SQLAlchemy + Alembic + Jinja2 스택으로 전환(그누보드6).
특히 Jinja2 유지 결정은 인쇄 템플릿 중심인 이 프로젝트와 방향 일치. → [[Gnuboard6]]

## 관련 페이지

- [[DeploymentArchitecture]] — 현재 이중 구조의 근원
- [[UniversityLeague]] — FastAPI 첫 적용 프로젝트
- [[Gnuboard6]] — FastAPI 방향 외부 검증
