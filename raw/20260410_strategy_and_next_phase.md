# 2026-04-10 전략 분석 및 다음 단계 계획

date: 2026-04-10
session: macbook_air (최종 세션)

---

## 1. 3일간 작업 분석 보고서 (4/8~4/10)

52회 회장기 현장 운영 중 발생한 30개 이상의 수정·배포 작업을 분석하여 반복 패턴과 근본 원인을 도출.

### 반복된 문제 패턴 6가지

**패턴 1 — web/ vs dist/ 이중 코드베이스 (10회 이상)**
- 거의 모든 수정에서 `web/`과 `dist/` 양쪽을 동시에 수정해야 함
- 한쪽만 수정 시 나중에 버그로 재발
- 8일: BYE 버그가 bracket.html에는 수정됐으나 main.js 미적용 → 재수정
- 근본 원인: SQLite(개발) vs MySQL(운영) 차이로 두 디렉토리 분리 유지

**패턴 2 — 브라우저 캐시 (4회)**
- 코드 수정·배포 후 화면 미반영 반복
- 매번 `?v=20260410b` 같은 수동 캐시버스팅 필요
- 해결 시도: Cache-Control 헤더, fetch cache:'no-store', 캐시버스팅 쿼리스트링

**패턴 3 — DB 환경 혼선 SQLite↔MySQL (2회)**
- 8일: 가상서버가 MySQL 아닌 SQLite 사용 중임을 작업 도중 발견
- 10일: 랭킹 API가 존재하지 않는 ranking.db(SQLite)를 바라봐 전체 불동작
- 두 번 모두 실제 서버 접속 전까지 미인지

**패턴 4 — 데이터 입력 오류 (1회, 중대)**
- 9일: 단체전 기록지를 단단복단복단단 순서로 출력했으나 실제 경기는 단단복복단단단
- 여자 32강 2~8번 경기 전체(49행) DB 직접 스왑으로 복구
- 시스템이 경기 방식을 사전 검증하지 않아 발생

**패턴 5 — UI 회귀 (2회)**
- 9일: GracketLite 전환 후 오더 등록/수정 버튼 소실
- 10일: DOM 렌더링 순서 오류 (null 에러)
- 최소한의 회귀 확인 체크리스트 부재로 배포 후 현장에서 발견

**패턴 6 — 멀티 디바이스 동기화 부담**
- 3대 PC + 가상서버, 단계가 많아 누락 가능성 상시
- 8일: macbook_air에서 sshpass 미설치 상태로 작업 시작

### 핵심 통찰
- 대부분의 고통은 기술 자체가 아닌 **구조 문제**에서 발생
- 이중 코드베이스, 캐시, DB 환경 불일치가 디버깅 시간의 절반 이상 차지
- 반면 현장 실시간 수정·배포 능력은 m4sellma 중앙화 + rsync + Claude Code 협업으로 가능

### 즉시 개선 항목
- `scripts/deploy.sh` — 6단계 배포를 1명령으로
- `docs/smoke_test.md` — 배포 전 체크리스트
- 일별 DB 자동 스냅샷

---

## 2. 기술 스택 방향 결정

### 현황
- Python Flask + Jinja2 (서버 렌더링 + API)
- Vanilla JavaScript 3,014줄 단일 파일 (main.js)
- CSS (인쇄 최적화 포함)
- SQLite(로컬) / MySQL(운영) 이중 DB
- Gunicorn + Ubuntu VPS

### 문제는 기술이 아닌 구조
web/dist 이중 수정, main.js 모노리스, 캐시 수동, DB 마이그레이션 없음 — 모두 구조 문제.
인쇄 템플릿, gracket-lite, tiebreak 알고리즘은 현재 스택에서 잘 작동.

### 결정: FastAPI + Alpine.js (점진적 전환)

**Flask → FastAPI 이유:**
- Pydantic으로 입력 검증 자동화 (경기 방식 오입력 방지)
- Alembic으로 마이그레이션 체계화 (수동 SQL 제거)
- /docs 자동 API 문서 (디버깅·현장 확인 편의)
- SQLAlchemy ORM (SQLite↔MySQL 전환 단순화)

**Vanilla JS → Alpine.js 이유:**
- 빌드 단계 없음 (CDN 한 줄, 오프라인 가능)
- 선언적 반응형 (window._tmGames 식 전역변수 제거)
- 기존 Jinja2 템플릿과 완전 호환
- main.js 3000줄 모노리스를 컴포넌트로 분리 가능

**유지할 자산 (교체 금지):**
- tiebreak.py — ITTF 규정 동률 처리 알고리즘
- draw.py — 시드 배치 로직
- gracket-lite.js — 경량 대진표 렌더러
- 인쇄 CSS (A4 최적화 노하우)
- SQLite 로컬 지원 (오프라인 대회장)

**비교 대안 검토:**
- Vue 3 + Vite: 빌드 필수, 오프라인 즉석 수정 불가, 전면 재작성 비용
- Django: ORM 장점은 있으나 과도한 구조
- 전면 재작성: 도메인 지식(인쇄, 경기 방식, 규정) 재구현 비용 과다

---

## 3. 그누보드6 컨텍스트

한국 최대 PHP BBS인 그누보드가 보안·성능·유지보수 이유로 PHP → Python/FastAPI 전환 완료(그누보드6).
그누보드6 스택: FastAPI + SQLAlchemy + Alembic + Jinja2 + Pydantic.
특히 Jinja2 유지는 인쇄 템플릿 중심인 이 프로젝트와 방향 일치.

현재 서버(community.sellma.kr)에 PHP 그누보드5가 운영 중.
향후 그누보드6으로 이전 시 같은 서버에 FastAPI 두 개 공존 구조.
대회 시스템은 도메인 특수성으로 그누보드 플러그인 통합보다 독립 FastAPI가 현실적.

---

## 4. 다음 프로젝트: 대학연맹 시스템

### 배경
- 52회 회장기 완주(~2026-04-12) 후 대학연맹 대회 운영 시스템 새로 시작
- 초등학교 대회와 다른 경기 방식 (대학 리그 특성)
- 처음부터 FastAPI로 설계

### 52nd에서 이식할 자산
- tiebreak.py (동률 처리)
- draw.py (시드 배치)
- gracket-lite.js (대진표 렌더러)
- 인쇄 템플릿 CSS 노하우
- 현장 UX 패턴 (오더 입력, 결과 저장, 자동 새로고침 흐름)

### 새로 설계할 것
- FastAPI + Alembic + Pydantic (처음부터)
- Alpine.js 프론트엔드 (빌드 없음)
- 단일 코드베이스 (web/dist 분리 없음)
- 경기 방식 사전 정의 선택 구조 (연맹별 프리셋)
- 선수 관리 CRUD UI
- 배포 스크립트 자동화

### 배포 목표
- ttresult.sellma.kr (신규 도메인)
- 그누보드6과 독립 공존
