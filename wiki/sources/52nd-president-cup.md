---
title: "제52회 회장기 전국 초등학교 탁구대회 운영 시스템"
type: source
tags: [table-tennis, tournament, flask, python, elementary-school, korea]
date: 2026-04-10
source_file: 52nd_president_cup/ (project directory)
---

## Summary

제52회 회장기(会長杯) 전국 초등학교 탁구대회를 위한 멀티페이즈 토너먼트 관리 시스템이다. 참가 선수 명단 엑셀 업로드부터 예선 리그, 본선 대진표, 단체전, 시상, 랭킹 집계까지 대회 운영 전 과정을 디지털화·자동화한다. Python/Flask 백엔드에 SQLite(로컬)/MySQL(서버) 이중 데이터베이스 구조를 사용하며, PWA로 모바일에서도 접근 가능하다.

## Key Claims

- **4단계 개발**: 1단계(예선 기록지 생성) → 2단계(본선 대진표) → 3단계(단체전·랭킹) → 4단계(서버 배포·인증)
- **10개 부문**: 남자1-2·3·4·5·6학년, 여자1-2·3·4·5·6학년
- **461명 참가**: `탁구 참가선수명단 엑셀.csv` 기준 422개 행 + 추가 명부
- **오프라인 지원**: SQLite 기반 로컬 실행(`start.sh`/`start.bat`), 서비스 워커 PWA 캐싱
- **KTT 공식 서식**: 한국탁구협회 표준 A4 기록지 형식 준수
- **ITTF 공방율 규칙**: `부록1 리그전시 순위 계산.pdf` 기준 동률 처리

## Key Quotes

> "SQLite(로컬) / MySQL(서버) 이중 운영" — 운영규칙.md

> "gunicorn 환경에서 마이그레이션 적용되려면 모듈 초기화 단계에서 init_db 호출해야 함" — feedback_init_db.md

## Connections

- [[KETTF]] — 대회 주최 기관, 한국초등탁구연맹
- [[52ndPresidentCup]] — 이 시스템이 운영하는 실제 대회 개체
- [[Sellma]] — 배포 도메인(sellma.kr) 및 동기화 허브(m4sellma) 운영 주체
- [[RoundRobinLeague]] — 예선 방식: 조별 리그전
- [[TieBreakRanking]] — 동률 처리 알고리즘: 승수 → 공방율 → 맞대결
- [[TournamentBracket]] — 본선 대진 생성 및 시드 배치 알고리즘
- [[TeamMatch]] — 3단계: 학교 대항 단체전 (6단식+1복식)
- [[ScoreSheet]] — KTT 표준 A4 기록지 생성
- [[DeploymentArchitecture]] — PythonAnywhere → VPS 배포 아키텍처
- [[PlayerVerification]] — 활동제한·이적 선수 검증 시스템

## Contradictions

- 없음 (최초 인제스트)
