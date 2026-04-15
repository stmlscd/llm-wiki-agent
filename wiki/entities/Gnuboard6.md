---
title: "Gnuboard6 (그누보드6)"
type: entity
tags: [fastapi, php, korea, bbs, open-source, validation]
sources: [20260410-strategy-next-phase]
last_updated: 2026-04-10
---

# Gnuboard6 (그누보드6)

한국 최대 PHP BBS 시스템인 그누보드가 보안·성능·유지보수 문제로 PHP에서 Python/FastAPI로 전환한 새 버전.

## 의미

- **검증 역할**: 한국 수십만 사이트가 사용하는 시스템이 FastAPI를 선택 → 이 프로젝트의 FastAPI 전환 방향 외부 검증
- **스택**: FastAPI + SQLAlchemy + Alembic + Jinja2 + Pydantic
- **특히 Jinja2 유지**: 인쇄 템플릿 중심인 이 프로젝트와 방향 일치

## 공존 관계

현재 community.sellma.kr 서버에 PHP 그누보드5 운영 중.
향후 그누보드6으로 이전 시 같은 서버에 FastAPI 두 개 공존:

```
community.sellma.kr
├── 그누보드6 (FastAPI) — 커뮤니티 BBS
└── 대회 시스템 (FastAPI) — 독립 운영
```

대회 시스템은 도메인 특수성으로 그누보드 플러그인 통합보다 독립 FastAPI가 현실적.
인증(로그인) 공유 정도만 연결하면 사용자 입장에서 통합된 것처럼 보임.

## 관련 페이지

- [[Sellma]] — 공통 서버 플랫폼
- [[TechStackMigration]] — FastAPI 전환 계획
- [[DeploymentArchitecture]] — 서버 구조
