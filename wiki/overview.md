---
title: "Overview"
type: synthesis
tags: [table-tennis, tournament, flask, python, korea]
sources: [52nd-president-cup]
last_updated: 2026-04-14
---

# Overview

*이 페이지는 모든 소스의 현재 합성 결과를 반영하여 인제스트마다 업데이트된다.*

---

## 현재 위키 범위

이 위키는 **제52회 회장기 전국 초등학교 탁구대회 운영 시스템** (`52nd_president_cup`) 프로젝트를 중심으로 구축되어 있다. 대회 운영 자동화를 위한 소프트웨어 시스템의 설계, 알고리즘, 배포 구조를 문서화한다.

---

## 핵심 주제

### 1. 대회 운영 자동화 (Tournament Automation)

[[52ndPresidentCup]]은 [[KETTF]]가 주최하는 2026년 전국 초등학교 탁구대회로, 461명이 참가한다. [[52nd-president-cup]] 시스템은 4단계에 걸쳐 발전하며 대회 전 과정을 디지털화한다:

- **1단계**: Excel 업로드 → 예선 기록지 자동 생성
- **2단계**: 동률 처리(공방율) + 본선 대진표 + 브래킷 시각화
- **3단계**: 학교 대항 단체전 + 전국 랭킹 집계
- **4단계**: VPS 서버 배포 + 로그인 인증 + PWA 모바일

### 2. 알고리즘 (Algorithms)

**[[RoundRobinLeague]]**: 예선 방식. 조 인원수(2~5인)별 고정된 경기 순서, JSON 형식의 게임별 스코어 저장.

**[[TieBreakRanking]]**: ITTF 규정 기반 동률 처리. 우선순위: ① 승수 ② 공방율(게임 수 기준) ③ 맞대결 공방율. 게임 패수가 0이면 ratio = 999.999(∞).

**[[TournamentBracket]]**: 조 수에 따라 16강/32강 자동 결정. 공식 시드 배치도(`시드번호 순서.pdf`) 기반 슬롯 매핑. BYE 자동 처리 및 승자 다음 라운드 자동 진출. `print_bracket.html` 오른쪽 절반 매치 인덱스 공식: `half // (2 ** (r + 1)) + mi + 1` (2026-04-11 수정; 구 공식은 존재하지 않는 매치 번호를 조회했음).

### 3. 산출물 (Outputs)

**[[ScoreSheet]]**: KTT 공식 A4 기록지. 27열 그리드, Jinja2 템플릿. 예선 앞장(결과 매트릭스 + 경기 일정표)과 본선 기록지 별도. **단체전 기록지 확정 구조(2026-04-11)**: 교보생명컵 기준, 경기당 5 sub-row(5.5mm/행). Adviser/Winner/Signature는 반드시 단일 tbody — 별도 tbody 사용 시 브라우저 분리 렌더링 버그 발생. 선수명+승수박스는 colspan=5 rowspan=5 단일 셀 통합.

**[[PrintDisplay]]**: 인쇄용 출력물 가독성 개선. 모든 템플릿 선수명·학교명 16pt 통일, `print_bracket.html` 두 줄 표시(`.box-name`+`.box-team` column flex), 단체전 gracket-lite 학교명 축약(`초등학교→초`). `is32` Jinja2 플래그로 32인/16인 대진표 별도 크기 적용. CSS 폰트 1~2pt 변경은 육안 식별 불가 — 최소 3pt 이상 차이 필요.

**[[TeamMatch]]**: 단체전 7경기(단식 6+복식 1). 연맹별 방식 다양화 지원 계획(4단1복/5단식/5단2복). **2026-04-14 세트 점수 검증 추가**: 프론트엔드+서버 이중 검증 — 탁구 11점제/듀스 규칙 위반 차단, 서버에서 `set_scores` 기반 세트 승수 재계산(프론트엔드 값 불신뢰). **2026-04-16 VPS 검증 배포 완료**: `_validate_set_score()` 함수 VPS routes_team.py에 추가, 위반 시 "규칙 위반입니다" 안내. **DB 주의**: 앱은 `kettf_tournament` DB 사용 — `kettf_db`와 혼동 금지.

### 4. 인프라 (Infrastructure)

**[[DeploymentArchitecture]]**: 3중 환경 — 로컬 SQLite(오프라인 대회장), PythonAnywhere(예선 공개 조회), VPS Ubuntu+MySQL(전체 관리). **[[Sellma]]** 플랫폼의 m4sellma를 중앙 코드 동기화 허브로 사용. **주의**: m4sellma rsync는 백업용이며 VPS에 자동 배포되지 않음 — VPS 배포는 `scp root@115.68.177.35:/var/www/kettf/2026/52nd/` 별도 실행 필요. Flask 템플릿은 `app.py` 위치의 `templates/` 폴더만 사용(`templates/templates/` 중첩 경로는 미사용).

**[[PlayerVerification]]**: 활동제한·이적 선수 자동 검출. 다단 CSV 파싱 시 `csv.reader` + 컬럼 인덱스 방식 필수.

---

## 알려진 설계 결정

| 결정 | 이유 |
|------|------|
| MySQL 통일 (SQLite 제거) | 52nd 현장에서 SQLite/MySQL 혼동 사고 방지 (2026-04-13 확정) |
| DB 연맹별 분리 | kettf_db(초등), kuttf_db(대학), ktta_db(협회), tt_result(시스템) — 혼동 방지 |
| 관리자 전용 기능 분리 | 결과 입력, 파일 업로드, 순위 계산은 로그인 필요 |
| 대회 관리 vs 기록 확인 분리 | 관리(IS_ADMIN=true) → 데이터 편집, 기록확인(IS_ADMIN=false) → 읽기 전용 |
| 공개 접근 허용 | 대회 기록 확인은 누구나 조회 가능 |
| gracket-lite.js 사용 | jQuery 의존 최소화, 경량 브래킷 렌더링 |
| 52nd API 호환 | main.js 수정 최소화 — API 응답을 52nd Flask와 동일 형식(직접 배열) |

---

**[[PlayerRanking]]**: MySQL `rk_*` 테이블(선수 380명, 대회 6개)에 연간 포인트 집계. MySQL 예약어 충돌로 순위 컬럼은 `rank_num` 사용. `web/`(SQLite) vs `dist/`+VPS(MySQL) 이중 환경 유지. 2026-04-10 마이그레이션 완료. **2026-04-12 회장기 포인트 반영 완료**: 4·5·6학년 291명에게 R1 등급 포인트 산출, 신규 39명 등록, rk_rankings 재계산. 엑셀 보고서(남자/여자) 생성. **2026-04-13 시도 수정 및 최종 마무리**: province 45건 수정(빈값/기타→정확한 시도), 랭킹 메뉴 버튼 삭제, VPS 최종완성 백업(7.1MB+322KB).

---

## 5. 현장 운영 교훈 (Infrastructure)

**[[FieldOperationLessons]]**: 4/8~10 3일간 30개 이상 수정·배포에서 도출된 6대 반복 패턴 + 4/11 추가 교훈.
① web/dist 이중 코드베이스 ② 브라우저 캐시 수동 관리 ③ SQLite↔MySQL 환경 혼선 ④ 데이터 입력 오류(게임 순서 스왑) ⑤ UI 회귀 ⑥ 멀티 디바이스 동기화.
핵심 통찰: **대부분의 고통은 기술 선택이 아닌 구조 문제**에서 발생.
**2026-04-11 추가**: ⑦ 코드 손상 시 디버깅보다 VPS tar.gz 백업 복원이 빠름 ⑧ sessionStorage 방식이 SPA 방식보다 현장에서 안정적. **수정완성본 백업 패턴** 도입 — `app_YYYYMMDD_HHMMSS_수정완성본.tar.gz` 명명으로 m4sellma에 저장. **시상 탭**: 남자단체/여자단체 서브탭 제거, 검사결과가 기본 탭.

---

## 기술 전환 결정

**[[TechStackMigration]]**: 52회 완주 후 Flask → FastAPI, Vanilla JS → Alpine.js 점진 전환 확정.
- **FastAPI 채택 이유**: Pydantic 입력 검증(경기 방식 오입력 차단), Alembic 마이그레이션(수동 SQL 제거), /docs 자동 문서화
- **Alpine.js 채택 이유**: 빌드 단계 없음(오프라인 가능), Jinja2 호환, 선언적 반응형
- **외부 검증**: [[Gnuboard6]] — 한국 최대 PHP BBS가 FastAPI+SQLAlchemy+Alembic+Jinja2로 전환

**유지할 자산 (교체 금지)**: tiebreak.py, draw.py, gracket-lite.js, 인쇄 CSS, SQLite 로컬 지원

---

## 다음 프로젝트 — 디자인 확정 (2026-04-14)

**[[UniversityLeague]]**: 탁구경기결과관리시스템(tt-result) 디자인 문서 APPROVED.
- **첫 실전**: 대학연맹 대회 **2026-04-28 ~ 05-02** (D-14 = 2026-04-14)
- **접근법**: Approach C (단계적 릴리스) — 모듈러 아키텍처 + 최소 기능 먼저
- **Phase 1 상태**: **개발 완료, 실전 테스트 진행 중**. m4sellma uvicorn(8100/8101 SSL) 운영 중
- **테스트 서버**: Mac mini(192.168.1.202:8100) → `http://www.sellma.kr/`
- **운영 서버**: VPS 115.68.177.35, MySQL kuttf_db (대회 시 배포)
- **남은 작업**: VPS 배포 + 실전 운영 테스트 마무리 (4/28 전까지)
- **Phase 2** (대회 후): 랭킹 통합, 시상, 코치 로그인, 선수 CRUD
- **Phase 3**: 중고/실업 연맹 확장, KTTA 연동

---

## 미해결 / 52nd 잔여 항목 (대학연맹으로 이관)

- 단체전 경기 방식(4단1복/5단식/5단2복) 사전 정의 선택 구조 → 대학연맹에서 처음부터 구현
- 랭킹 UI 보강 (학년별·성별 순위표 프론트엔드)
- 코치/팀 로그인 포털
- 선수 관리 CRUD UI (SSH 없이 관리자 페이지에서 추가/수정)
