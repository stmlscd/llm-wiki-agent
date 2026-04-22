---
title: "ServerRules"
type: concept
tags: [rules, infrastructure, governance]
sources: [20260423-server-rules-and-adoption-policy, 20260423-m4sellma-hot-standby]
last_updated: 2026-04-23
---

## 정의
tt-result 프로젝트의 서버 역할과 데이터 동기화 원칙을 규정하는 최상위 운영 규칙. 2026-04-23 제정.

## 규칙
1. **운영 서버 = VPS (115.68.177.35)** — 실 운영 트래픽 기본.
2. **백업·테스트 서버 = m4sellma (192.168.1.202, Mac mini M4)** — 3역할 겸용:
   - VPS 데이터 상시 복제 (핫 스탠바이)
   - VPS 중지 시 즉시 대체
   - 신기능·배포 전 테스트
3. **상시 동일 데이터 유지** — 매일 02:00 `scripts/vps_to_m4_sync.py` 자동 sync.
4. 대회 진행 중(D-0~D+4) 자동 sync 금지, 수동만.
5. 운영 VPS DB 직접 ALTER/UPDATE 금지 — Alembic 마이그레이션만.
6. m4sellma 앱은 로컬 MySQL 사용 (`APP_SSH_TUNNEL=false`).
7. 장애 전환 목표 시간 **3분 이내**.

## 근거
- 이전 m4sellma 구조(SSH 터널로 VPS MySQL 참조)는 VPS 장애 시 함께 죽어 백업 의미 상실 — 2026-04-23 리허설에서 발견.
- 대회 운영은 **시간이 고정된 이벤트** 라 장애 복구 시간을 3분 내로 제약.
- 외부 VPS 단일 의존은 리스크 — 로컬 복제 필수.

## 관련 자산
- `docs/server-rules.md` — 공식 규칙 문서 (6개 섹션)
- `CLAUDE.md §16` — 매 세션 자동 로드 요약
- 스크립트: `scripts/vps_to_m4_sync.py`, `scripts/m4sellma_mysql_setup.py`, `scripts/m4sellma_switch_to_local.py`
- 인프라: m4sellma MySQL 9.6, uvicorn 3 포트 (8100·8101·8102) via screen

## Connections
- [[DeploymentArchitecture]] — 물리 구조
- [[Sellma]] — 운영자 및 도메인 보유자
- [[AdoptionPolicy]] — 이 규칙과 쌍을 이루는 도구 도입 정책
- [[FieldOperationLessons]] — 현장 관점의 규칙 필요성
