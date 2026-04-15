---
title: "UniversityLeague (대학연맹 대회 시스템)"
type: entity
tags: [project, fastapi, university, table-tennis, next-phase]
sources: [20260410-strategy-next-phase, 20260412-fastapi-migration-kickoff, 20260413-university-league-build]
last_updated: 2026-04-13
---

# UniversityLeague (대학연맹 대회 시스템)

52회 회장기 완주 후 시작할 다음 프로젝트. 대학 탁구 연맹 대회 운영 시스템.

## 개요

- **시작 시점**: 2026-04-12 착수 (52회 회장기 완주 당일)
- **대상**: 대학 탁구 연맹 대회 (초등학교 대회와 경기 방식 상이)
- **배포**: www.sellma.kr (Mac mini 테스트), tt-result VPS (운영)
- **기술 스택**: FastAPI + Alembic + Pydantic + Alpine.js + SQLAlchemy
- **DB**: kuttf_db (MySQL, VPS 115.68.177.35)
- **상태**: Phase 3 완료. 대회관리 7탭(단체전/개인전/복식/혼합/선발전/일정/검사), 엑셀 업로드 파서 구현
- **제44회 연맹전**: 참가신청명단 업로드 완료 (118명 단체, 124명 개인, 107조 복식)

## 52nd에서 이식할 자산

| 자산 | 이식 방법 |
|------|-----------|
| `tiebreak.py` | 직접 이식 (알고리즘 재사용) |
| `draw.py` | 직접 이식 (시드 배치 재사용) |
| `gracket-lite.js` | 직접 이식 (대진표 렌더러) |
| 인쇄 CSS 노하우 | 재적용 (A4 최적화 패턴) |
| 현장 UX 패턴 | 설계에 반영 (오더 입력, 결과 저장, 자동 새로고침) |

## 새로 설계할 것

- **FastAPI 기반**: 처음부터 Alembic 마이그레이션, Pydantic 검증
- **Alpine.js 프론트**: 빌드 없는 반응형 UI, 단일 코드베이스
- **경기 방식 선택 구조**: 연맹별 프리셋 사전 정의 (4단1복/5단식/5단2복 등)
- **선수 관리 CRUD UI**: SSH 없이 관리자 페이지에서 선수 추가/수정
- **배포 자동화**: `scripts/deploy.sh` 처음부터 포함
- **단일 코드베이스**: web/dist 분리 없음

## 실행계획 타임라인 (Phase 0~9, 약 21일)

| Phase | 내용 | 기간 |
|-------|------|------|
| 0 | 인프라 준비 ✅ | 0.5일 |
| 1 | 프로젝트 골격 + DB ✅ | 1~2일 |
| 2 | 인증 시스템 | 0.5일 |
| 3 | 비즈니스 로직 이식 + 테스트 ★ | 2~3일 |
| 4 | 예선리그 API + 프론트 | 2일 |
| 5 | 순위/추첨/대진표 | 2~3일 |
| 6 | 단체전 | 2~3일 |
| 7 | 시상/인쇄 | 1일 |
| 8 | 신규 기능 | 3~4일 |
| 9 | 배포 + 검증 | 1~2일 |

## 네트워크 구성

- **테스트**: www.sellma.kr → UniFi 80/8843 → Mac mini(192.168.1.202:8100)
- **DuckDNS**: sellma0080.duckdns.org (KT DHCP IP 자동 업데이트)
- **운영**: tt-result → VPS 115.68.177.35

## 관련 페이지

- [[52ndPresidentCup]] — 전신 프로젝트, 도메인 지식 원천
- [[TechStackMigration]] — 적용할 기술 전환 계획
- [[DeploymentArchitecture]] — Mac mini 테스트 환경 추가
- [[KETTF]] — 초등 연맹 (대학연맹과 구분)
- [[Sellma]] — 배포 플랫폼
