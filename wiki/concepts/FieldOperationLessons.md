---
title: "FieldOperationLessons (현장 운영 교훈)"
type: concept
tags: [lessons-learned, operations, deployment, debugging, tournament]
sources: [20260410-strategy-next-phase]
last_updated: 2026-04-16
---

# FieldOperationLessons (현장 운영 교훈)

52회 회장기 현장 3일(2026-04-08~10) 운영에서 도출된 반복 패턴과 개선 방향.

## 6대 반복 패턴

### 1. 이중 코드베이스 (web/ vs dist/)
- **빈도**: 10회 이상
- **증상**: web/과 dist/ 양쪽 동시 수정 필요, 한쪽 누락 시 버그 재발
- **원인**: SQLite(개발) vs MySQL(운영) 차이로 두 디렉토리 분리 유지
- **해결**: 환경변수 `USE_MYSQL=1` 단일화 후 코드베이스 통합

### 2. 브라우저 캐시
- **빈도**: 4회
- **증상**: 코드 배포 후 화면 미반영, 시크릿 탭에서만 반영 확인
- **원인**: 정적 파일 버전 수동 관리
- **해결**: Jinja2에서 파일 해시를 자동으로 `?v=` 파라미터에 주입

### 3. DB 환경 혼선
- **빈도**: 2회
- **증상**: 코드는 MySQL 가정, 실제 서버는 SQLite (또는 반대)
- **해결**: 단일 코드베이스 + SQLAlchemy ORM으로 DB 추상화

### 4. 데이터 입력 오류 (중대)
- **빈도**: 1회
- **증상**: 단체전 게임 순서 오입력(단단복단복단단 vs 단단복복단단단) → 49행 DB 스왑
- **해결**: 경기 방식 Pydantic Enum 검증 + 오더 확정 전 확인 모달

### 5. UI 회귀
- **빈도**: 2회
- **증상**: 대형 리팩터 후 기존 기능 조용히 소실 (버튼 사라짐, null 에러)
- **해결**: `docs/smoke_test.md` 배포 전 체크리스트

### 6. 멀티 디바이스 동기화
- **빈도**: 상시
- **증상**: 3대 PC + VPS, sshpass 미설치 등 환경 불일치
- **해결**: `docs/setup_new_device.md` 표준화 + `scripts/deploy.sh` 원스텝 배포

## 2026-04-11 추가 교훈

### 7. 코드 손상 시 디버깅보다 백업 복원이 빠름
- **사례**: 브래킷 결과 반영 시도 중 main.js 전체 손상
- **해결**: 오늘자 손상된 백업 대신 4월 10일 VPS tar.gz 직접 복원
- **원칙**: 복잡한 diff 대신 신뢰할 수 있는 스냅샷으로 복원 후 최소 수정
- **백업 위치**: m4sellma `/Volumes/WorkSpace/project_src/claudespace/52nd_backup/`

### 8. sessionStorage 새로고침이 SPA 방식보다 안정적
- SPA 방식(`loadBracketSection` 직접 호출)은 상태 관리 복잡도 증가
- sessionStorage + `location.reload()` 방식은 단순하고 탭/성별/학년 상태가 확실히 복원됨
- 현장 운영에서는 단순한 방식이 우선

### 9. tbody 분리가 테이블 레이아웃을 깨뜨림 (2026-04-11)
- **사례**: `print_team_scoresheet.html`의 Adviser/Winner/Signature가 각각 별도 `<tbody>`, 사이에 `bl-body-td` 구분선 tbody → 브라우저가 Winner/Signature를 오른쪽으로 분리 렌더링
- **해결**: 세 행을 하나의 `<tbody>` 안에 연속 배치, `bl-body-td` tbody 제거
- **원칙**: 시각적 구분이 필요하면 CSS border 사용, tbody 분리는 페이지 브레이크 제어 목적 외에 사용하지 않는다

### 10. MindVault 훅이 Windows에서 커맨드 창 깜빡임 유발 (2026-04-16)
- **사례**: Claude Code 작업 중 커맨드 창이 반복적으로 깜빡거림
- **원인**: `~/.claude/settings.json`에 PostToolUse 훅 2개(mindvault-lore-hook.sh, mindvault-rules-hook.sh)가 Bash/Edit/Write 실행마다 python3, mindvault.exe 등 콘솔 프로세스를 호출 → Windows에서 콘솔 창이 순간 생성/소멸
- **해결**: PostToolUse 훅 제거, UserPromptSubmit 훅(mindvault-hook.sh)만 유지 — 프롬프트 제출 시 1회만 실행되므로 깜빡임 최소화
- **원칙**: Windows 환경에서 빈번한 PostToolUse 훅은 콘솔 깜빡임을 유발하므로, 꼭 필요한 훅만 UserPromptSubmit에 배치한다

## 긍정적 교훈

- m4sellma rsync 중앙화로 현장 실시간 수정·배포 가능
- Claude Code 협업으로 복잡한 버그(BYE 렌더링, 연결선 수식)를 당일 해결
- 운영규칙.md + 대화기록 누적으로 컨텍스트 손실 없이 3일 연속 운영
- VPS tar.gz 백업 주기적 생성으로 코드 손상 시 빠른 복원 가능 (수정완성본 태그 활용)

## 다음 대회를 위한 즉시 구현 목표

| 항목 | 해결 패턴 |
|------|-----------|
| `scripts/deploy.sh` | 패턴 1, 6 |
| `docs/smoke_test.md` | 패턴 5 |
| 일별 DB 스냅샷 자동화 | 패턴 4 (복구 대응) |
| 캐시 버스팅 자동화 | 패턴 2 |
| 경기 방식 프리셋 잠금 | 패턴 4 (예방) |

## 관련 페이지

- [[DeploymentArchitecture]] — 현재 배포 구조
- [[TechStackMigration]] — 패턴 해소를 위한 기술 전환
- [[TeamMatch]] — 패턴 4(입력 오류)와 직접 연관
