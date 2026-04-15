---
title: "DeploymentArchitecture (배포 아키텍처)"
type: concept
tags: [deployment, flask, gunicorn, mysql, sqlite, pythonanywhere, vps, pwa]
sources: [52nd-president-cup, 20260412-fastapi-migration-kickoff]
last_updated: 2026-04-12
---

# DeploymentArchitecture (배포 아키텍처)

[[52nd-president-cup]] 시스템의 3중 배포 환경: 로컬 SQLite, PythonAnywhere 공개 서버, VPS 프로덕션 서버.

## 환경별 구성

### 1. 로컬 개발 (dist/ 패키지)

```
MacBook Air / Windows PC
  └─ dist/ (43KB zip 번들)
       ├─ app.py
       ├─ tournament.db (SQLite)
       ├─ start.sh / start.bat
       └─ http://localhost:5000
```

- **DB**: SQLite3
- **서버**: Flask 내장 개발 서버
- **용도**: 오프라인 대회장 운영

### 2. PythonAnywhere (예선 공개 서버)

```
sellma0080.pythonanywhere.com
  └─ /home/sellma0080/kettf/2026/52nd_president_cup/
       ├─ tournament.db
       └─ WSGI → wsgi.py
```

- **특징**: 로그인 불필요, 예선 리그 결과 조회만 허용
- **단계**: 1단계·2단계 운영

### 3. VPS 프로덕션 (community.sellma.kr)

```
Ubuntu 24.04 LTS (IP: 115.68.177.35)
  └─ /var/www/kettf/2026/52nd/
       ├─ MySQL 8.0
       ├─ gunicorn (2 workers, port 8000)
       ├─ Apache2 (SSL 역방향 프록시)
       └─ systemd (자동 재시작)
```

- **DB**: MySQL 8.0 (SQLite에서 마이그레이션)
- **단계**: 4단계, 전체 기능 (관리자 로그인 필요)

## 기술 스택 요약

| 계층 | 기술 |
|------|------|
| 언어 | Python 3.10+ |
| 웹 프레임워크 | Flask 3.0.3 |
| DB (로컬) | SQLite3 |
| DB (서버) | MySQL 8.0 + pymysql |
| WSGI | gunicorn 21+ |
| 리버스 프록시 | Apache2 + SSL |
| 프로세스 관리 | systemd |
| 엑셀 처리 | openpyxl 3.1.5 |
| 패스워드 해싱 | Werkzeug |
| 브래킷 UI | gracket-lite.js (jquery.gracket.js 기반) |
| PWA | manifest.json + Service Worker |

## 주요 주의사항

### init_db 호출 위치
gunicorn 멀티 워커 환경에서 DB 마이그레이션이 적용되려면 **모듈 초기화 단계**(앱 import 시)에서 `init_db()`를 호출해야 함. 라우트 핸들러 내부에서 호출하면 일부 워커에서 누락될 수 있음.

### SQLite → MySQL 마이그레이션
- `JSON` 컬럼: SQLite `TEXT` → MySQL `JSON` 타입
- 자동증가 PK: `AUTOINCREMENT` → `AUTO_INCREMENT`
- 한국어 인코딩: `utf8mb4` charset 명시 필수

## PWA 모바일 지원

- `dist/static/manifest.json` — 앱 아이콘(192px, 512px), 이름, 테마색
- `dist/static/sw.js` — 서비스 워커 (오프라인 캐싱)
- QR코드로 모바일 빠른 접속

## 동기화 규칙

작업 전: `rsync pull from m4sellma`
작업 후: `rsync push to m4sellma`
배포 시: `scp 선택 파일 → community.sellma.kr`

## Flask 템플릿 경로 주의 (2026-04-11)

프로젝트에 `templates/templates/`(구버전, 미사용)와 `templates/`(실사용) 이중 경로 혼재.
Flask는 `app.py` 위치의 `templates/` 서브폴더만 읽음 — 중첩된 `templates/templates/`는 사용되지 않음.

- **실사용 경로**: `dist/templates/`, `web/templates/`
- **미사용 구버전**: `dist/templates/templates/`, `web/templates/templates/`

수정 시 반드시 `templates/` 직접 경로 파일을 수정할 것.

## VPS 배포 주의 (2026-04-11)

m4sellma rsync는 **백업 전용** — VPS에 자동 배포되지 않음.
VPS 배포는 항상 별도 scp 필요:

```bash
scp <로컬 파일> root@115.68.177.35:/var/www/kettf/2026/52nd/templates/
```

## 수정완성본 백업 패턴 (2026-04-11)

주요 기능 완성 시점마다 VPS 전체를 tar.gz으로 m4sellma에 백업:

```bash
# VPS에서 tar 생성
ssh root@115.68.177.35 "cd /var/www/kettf/2026 && \
  tar --exclude='52nd/*.db' --exclude='52nd/backup_*' \
  -czf /tmp/app_YYYYMMDD_HHMMSS_수정완성본.tar.gz 52nd/"

# m4sellma로 업로드
scp /tmp/app_...tar.gz m4sellma@ftp.sellma.kr:/Volumes/WorkSpace/project_src/claudespace/52nd_backup/
```

**백업 위치**: m4sellma `/Volumes/WorkSpace/Project_src/claudespace/52nd_president_cup/backup/`
**명명 규칙**: `app_YYYYMMDD_HHMMSS_태그.tar.gz`

### 2026-04-12 최종 백업 (대회 종료)
- **VPS 소스**: `backup/20260412/vps_52nd_20260412.tar.gz` (2.8MB)
- **MySQL dump**: `backup/20260412/kettf_tournament_20260412.sql` (316KB)
- **Mac 스냅샷**: `app_20260412_final.tar.gz` (3.7MB) — dist/web/docs/scripts/skills

코드 손상 시 최신 수정완성본 tar.gz에서 복원 → 최소 수정으로 빠른 복구.

## Windows ↔ Mac 동기화 덮어쓰기 위험

Windows PC → Mac scp 동기화 실행 시 Windows 구버전이 Mac 수정 파일을 덮어씀.
동기화 후 반드시 재수정 + VPS 재배포 필요. 브래킷 공식 수정은 Windows 원본에도 적용할 것.

## 4. Mac mini 테스트 서버 (www.sellma.kr, 2026-04-12~)

```
Mac mini (m4sellma, 192.168.1.202)
  └─ /Volumes/Data/WWW/university-league/
       ├─ Python 3.9 venv + FastAPI
       ├─ uvicorn :8100 (--reload)
       ├─ tournament.db (SQLite)
       └─ 외부: www.sellma.kr:80, :8843
```

- **용도**: university-league FastAPI 개발 테스트
- **네트워크**: KT DHCP → DuckDNS(sellma0080) → UniFi 포트포워딩
- **배포**: Windows → paramiko SFTP (scripts/deploy_to_mac.py)
- **SSD**: /Volumes/Data (테스트), /Volumes/WorkSpace (백업/동기화)

## 5. VPS 운영 서버 (tt-result, 예정)

```
Ubuntu 24.04 LTS (115.68.177.35)
  └─ tt-result DNS (예정)
       ├─ Nginx reverse proxy :80 → uvicorn :8000
       ├─ university-league FastAPI
       └─ 기존 community.sellma.kr:8000 (52nd) 유지
```

## Connections

- [[Sellma]] — 인프라 운영 주체
- [[52ndPresidentCup]] — 운영 대상 대회 (52nd)
- [[UniversityLeague]] — 새 프로젝트 (Mac mini + VPS)
- [[PlayerVerification]] — 서버 기능
- [[52nd-president-cup]] — 구현 소스
