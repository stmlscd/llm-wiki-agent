# 2026-04-12: FastAPI 마이그레이션 착수 — 계획 수립 + 인프라 준비

## 배경
제52회 회장기 대회 종료일(4/12). Mac(m4sellma)에서 현장 작업한 내용을 Windows PC로 역동기화 완료 후, 대학연맹 시스템(university-league) FastAPI 마이그레이션을 본격 착수.

## 주요 작업

### 1. Mac → Windows 역동기화
- Mac(192.168.1.202:22)에서 paramiko SFTP로 3개 tar.gz 다운로드
  - llm-wiki-sync.tar.gz (41KB): wiki concepts/sources/entities/syntheses/raw
  - 52nd-sync.tar.gz (3.4MB): dist/web/docs/scripts/skills
  - claude-memory-sync.tar.gz (2.7KB): Claude 메모리
- Mac Claude 메모리 → Windows 메모리 동기화 (4개 신규 추가)
- Windows PC(F:\52nd_president_cup)가 메인 소스로 확정

### 2. FastAPI 마이그레이션 실행계획 수립
- 두 문서 기반: 탁구경기결과관리시스템-계획.md + 준비단계.md
- Phase 0~9 총 21일 계획
- 핵심 원칙: 검증된 로직(tiebreak, draw, tournament) 절대 재작성 금지, I/O 레이어만 변경
- 프론트엔드: main.js 3000줄 모노리스 → 6개 Alpine.js x-data 컴포넌트 분리
- 멀티테넌시: 모든 테이블에 league_id FK 포함 (elementary/university/ktta)

### 3. Mac mini 테스트 환경 구축 (Step 0-2)
- /Volumes/Data/WWW/university-league/ 생성
- Python 3.9 venv + FastAPI 의존성 31개 설치
- 프로젝트 골격 22파일 배포 (ORM 모델 11개, config, database, main)
- uvicorn 기동 테스트 완료 (port 8100)

### 4. 네트워크 구성 (Step 0-2b)
- UniFi 포트포워딩: 80 → Mac:8100, 8843 → Mac:8100
- DuckDNS: sellma0080.duckdns.org
- sellma.kr → DuckDNS CNAME 연결
- 외부 접속 확인: http://www.sellma.kr/ 정상 응답
- UniFi Controller: 443 포트 (Google OAuth 로그인, API 직접 인증 불가)

### 5. 52nd 시스템 스냅샷 (Step 0-3)
- app_20260412_final.tar.gz (3.7MB) Mac에 백업 완료

### 6. Alembic 초기화 (Phase 1 일부)
- alembic init + autogenerate로 11테이블 초기 마이그레이션 생성
- alembic upgrade head 적용 완료

## 프로젝트 구조
```
university-league/
├── pyproject.toml          # FastAPI 의존성
├── alembic.ini + alembic/  # DB 마이그레이션
├── .env                    # 환경변수
├── app/
│   ├── main.py             # FastAPI app
│   ├── config.py           # pydantic-settings
│   ├── database.py         # SQLAlchemy engine
│   ├── models/ (11개)      # ORM: leagues, players, results, tiebreak_results,
│   │                       #   draw_results, tournament_matches, team_players,
│   │                       #   team_draw, team_matches, team_game_details, users
│   ├── schemas/            # (Phase 2~에서 추가 예정)
│   ├── routers/            # (Phase 2~에서 추가 예정)
│   ├── services/           # (Phase 3에서 이식 예정)
│   └── auth/               # (Phase 2에서 추가 예정)
├── static/css/, js/        # (Phase 4~에서 추가 예정)
├── scripts/
│   ├── deploy_to_mac.py    # Windows → Mac 원클릭 배포
│   ├── unifi_portforward.py # UniFi API 포트포워딩
│   └── duckdns_update.sh   # DuckDNS IP 자동 업데이트
└── tests/                  # (Phase 3에서 추가 예정)
```

## 네트워크 토폴로지
- Windows PC (메인 개발) → paramiko SFTP → Mac mini (테스트 서버)
- 외부: www.sellma.kr → DuckDNS → KT DHCP IP → UniFi 80/8843 → Mac:8100
- VPS: 115.68.177.35 (tt-result DNS 예정, 운영 서버)

## 다음 단계
- 사용자가 밑그림(UI/UX 설계) 완성 후 Phase 2(인증) → Phase 3(비즈니스 로직 이식) 진행 예정
- Phase 3이 가장 핵심: tiebreak.py, draw.py, tournament.py I/O 레이어 변환 + 테스트
