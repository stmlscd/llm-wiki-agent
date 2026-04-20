# 2026-04-20 세션 원본 기록 — tt-harness-kit 배포 + 인프라 정비

> 이 문서는 M1 Max 에서 Claude Code 세션으로 진행된 작업의 raw 원본 기록이다.
> 요약/지식 페이지는 `wiki/sources/20260420-tt-harness-kit-deployment.md` 를 참조.

## 세션 맥락

- **작업자**: 상철 + Claude (M1 Max Claude Code 세션)
- **시각**: 2026-04-20 09:30~10:55 KST
- **대상 프로젝트**: tt-result (탁구경기결과관리시스템, 제52회 회장기 대회 운영 중)
- **주요 입력 자료**:
  - 영상 `이게 왜 무료임 하이퍼 프레임!! 오픈클로에서 영상 생성하기 - 1080p.mp4`
  - 압축 `ohmyclaw-main.zip` (OpenClaw 멀티프로바이더 에이전트 하네스 스킬)
  - URL `https://jkf87.github.io/openclaw-docs-ko/start/setup`

## 작업 흐름

### Phase 1 — 외부 자료 분석
1. 영상 mp4 → whisper 트랜스크립트 (`이게 왜 무료임 하이퍼 프레임!!.srt` / `.txt`)
   - 내용: Pagen 사의 Hyperframe (AI 에이전트용 비디오 생성 도구) 소개, OpenClaw 에 바로 붙여 쓸 수 있음
2. openclaw-docs-ko 사이트 → 424 MD 파일 통합본 `openclaw-docs-ko-merged.md` (3.6MB)
3. ohmyclaw-main.zip 분석 → `ohmyclaw-분석-보고서.md`
   - 저자: jkf87, MIT 라이선스
   - 핵심: routing.json 단일 소스 + jq 기반 결정론적 라우터, 다중 계정 풀, 5유형 갭 감지, Plan→Work→Review 파이프라인, OMX 스타일 composable verbs

### Phase 2 — tt-harness-kit 이식
ohmyclaw 에서 tt-result 스택(FastAPI/Python/Alpine.js, 솔로 개발자)에 적합한 부분만 추출:

**이식한 것** (4개):
- `tt-plan` — 계획 수립 (docs/plans/ 저장)
- `tt-work` — 자율 실행 + Lore 커밋 포맷
- `tt-review` — 5관점 리뷰 + 갭 감지 5유형
- `tt-ralph` — executor+verifier 끝까지 루프 (최대 5회)
- `tt-consensus` — planner→architect→critic 3단 합의
- `scripts/tt-doctor.sh` — 10항목 환경 점검
- `scripts/deploy-kettf.sh` — 배포 자동화
- `docs/gap-detection-patterns.md` — tt-result 예시 5×5

**제외한 것**:
- 모델 라우팅 (solo 개발자엔 과잉)
- 다중 계정 풀 (불필요)
- 브릿지 실시간 알림 (OpenClaw 전용)
- jq/bash 라우팅 엔진 의존

### Phase 3 — 배포 (4-tier 인프라)
1. **윈컴** (`F:\52nd_president_cup\`, amd_sellma02): SMB 마운트(`//amd.sellma01:****@192.168.1.246/F`) → `.claude/commands/` 5 파일 + `scripts/tt-doctor.sh` 설치 → `git commit 3fcf566` + push
2. **M1 Max** (`/tmp/ttr-clone/`): gh repo clone → 작업 허브 역할
3. **m4sellma** (`/Volumes/WorkSpace/claudespace/tt-result/`): rsync 70MB fresh git clone 전송 → doctor PASS 16/WARN 6/FAIL 7 (FAIL 은 Python 의존성 미설치로 예상)
4. **VPS** (`/var/www/tt-result/`, 115.68.177.35):
   - SSH-over-HTTPS (port 443) 설정 (outbound 22 차단 우회)
   - GitHub deploy key 등록 (read-only)
   - git init + remote add + fetch + `git reset --mixed origin/main` (safe, working tree 무변경)
   - 커맨드 5 파일 + tt-doctor.sh rsync (서비스 무관)

### Phase 4 — 인프라 분석 및 안전장치
1. **VPS에 두 스택 공존 발견**:
   - Flask (레거시): `/var/www/kettf/2026/52nd/` (gunicorn, port 8000, `kettf-tournament.service`) ★ 대회 운영 중
   - FastAPI (신규): `/var/www/tt-result/` (uvicorn, port 8100, `tt-result.service`)
2. **배포 매핑 문서화** `docs/deployment-map.md`: 대회 기간 주의사항 포함
3. **배포 스크립트 작성** `scripts/deploy-kettf.sh`:
   - 환경변수 override (KETTF_REPO, KETTF_DST, KETTF_SERVICE, SKIP_SYSTEMD, SKIP_GIT_PULL)
   - 런타임 파일 보호 (*.db, __pycache__, backup/, venv/, .env, uploads)
   - dry-run 모드
4. **보안 감사** `docs/security-audit-2026-04-20.md`:
   - 🚨 CRITICAL: `52nd_president_cup/web/db.py` 에 MySQL 자격증명 하드코딩 (`sellma0080` / 평문 `Ccoals4096#%#%`)
   - HIGH: VPS `.env` 파일 권한/gitignore 확인 필요
   - MEDIUM: `docs/서버.md` 에 VPS root 평문 비번 `P@0SY5C^`
   - MEDIUM: `/var/www/tt-result/` ownership UID 501 staff (macOS 스타일)

### Phase 5 — 배포 테스트 + 실 배포 실패 + 자동 롤백
1. **m4sellma 검증** (SKIP_SYSTEMD=1, 가짜 src/dst): 런타임 파일 5종 보존, 코드 4종 동기화 → PASS
2. **VPS dry-run 1차**: `player_register_2026/*.csv`, `migrate_*.py`, `check_integrity.py`, `admin_users.html` 삭제 예정 발견 → exclude 목록 보강
3. **VPS dry-run 2차**: 삭제 0개 확인 → 실 배포 결정
4. **VPS 실 배포** (`2026-04-20 10:51 KST`):
   - 백업: `/var/www/kettf/2026/52nd.backup-20260420-105134` (28MB)
   - rsync 성공
   - `systemctl reload-or-restart kettf-tournament.service` → `gunicorn exit code 4, Reason: App failed to load`
   - **자동 롤백 실행** → HTTP 200 정상 복구, 다운타임 ~5초
5. **장애 원인 확정**: github `52nd_president_cup/web/` ↔ VPS `/var/www/kettf/2026/52nd/` 는 **완전히 다른 코드베이스**
   - VPS wsgi.py: `from app import app` / VPS app.py: admin 로그인 시스템
   - github wsgi.py: `from app import app as application` / github app.py: sqlite ranking DB 연동
   - 두 코드베이스 독립 진화 → `deploy-kettf.sh` 의 매핑 전제가 틀림

### Phase 6 — 대회 후 계획 기록
`docs/post-tournament-tasks.md` 작성:
- **P0 보안** (Day 1): MySQL 비번 교체, VPS root 비번 교체, db.py → .env 리팩토링, git history 비밀 제거
- **P1 배포 정합성** (Day 2-3): VPS kettf ↔ github 매핑 재정립 (옵션 A: VPS → github 역동기화 권장)
- **P2 구조 정리** (Day 4-5): 재귀 디렉토리 (`server/server/server/...`, `templates/templates/templates/...`) 제거, ownership root:root 정정, 한글 파일명 NFD→NFC
- **P3 개선** (Week 2+): CI/CD, 모니터링

## 세션 중 발생한 주요 이벤트

| 시각 | 이벤트 |
|------|--------|
| 09:30 | 세션 시작, 영상+ohmyclaw.zip+URL 분석 의뢰 |
| 09:35 | whisper mlx 설치, 영상 트랜스크립트 추출 완료 |
| 09:40 | openclaw-docs 424 파일 통합, ohmyclaw 분석 보고서 작성 |
| 09:50 | tt-harness-kit 설계 + M1 Max 빌드 |
| 10:00 | SMB 마운트 → 윈컴 tt-result 경로(F:\52nd_president_cup) 확인 + 설치 |
| 10:10 | GitHub push 완료 (`3fcf566 feat: tt-harness-kit 도입`) |
| 10:25 | m4sellma SSH 키 등록 완료 |
| 10:30 | VPS SSH 접근 + deploy key 등록 |
| 10:40 | VPS git tracking 활성화 (SSH-over-HTTPS) |
| 10:45 | 배포 스크립트 리팩토링 + m4sellma 테스트 통과 |
| 10:51 | VPS 실 배포 시도 → gunicorn 장애 → 자동 롤백 (~5초 다운) |
| 10:53 | 장애 원인 진단: github ↔ VPS 코드 베이스 불일치 |
| 10:55 | 대회 후 과제 문서화 + deployment-map 교정 |

## 학습된 원칙 (상철님이 세션 중 표명)

1. **"m4sellma와 github에는 항상 동기화"** — commit → push → rsync 를 단일 흐름으로
2. **대회 중 파괴적 변경 금지** — 문서/스크립트만 추가, 서비스 다운 위험한 건 대회 후
3. **"기억 DB todtjdgksrjwl (생성한거지)"** — M1 Max 클로드도 자체 기억 시스템 보유 확인

## 접근 경로 정리 (세션 중 확립됨)

| 대상 | 접근 방법 | 비번 필요 여부 |
|------|----------|----------------|
| GitHub stmlscd/tt-result | `gh` CLI (keyring 인증) | ❌ |
| m4sellma (192.168.1.179) | `ssh m4sellma` (ed25519 키 등록) | ❌ |
| VPS (115.68.177.35) | `ssh vps-tt` (ed25519 키 등록) | ❌ |
| 윈컴 (192.168.1.246) | SMB `//amd.sellma01@.../F` (Windows 공유) | 비번 (2 공백) 필요 |
| VPS→GitHub | SSH over HTTPS (`ssh.github.com:443`), deploy key | ❌ |

## 파일 생성 목록

### tt-result repo (ea3cbad 포함)
- `.claude/commands/tt-{plan,work,review,ralph,consensus}.md` (5 파일)
- `scripts/tt-doctor.sh`
- `scripts/deploy-kettf.sh`
- `52nd_president_cup/web/.env.example`
- `docs/deployment-map.md`
- `docs/security-audit-2026-04-20.md`
- `docs/post-tournament-tasks.md`

### M1 Max 로컬 (`/Users/m1.max/claudespace/`)
- `openclaw-docs-ko-merged.md` (3.6MB, 424 문서 통합)
- `ohmyclaw-분석-보고서.md` (16KB)
- `하이퍼프레임-영상-트랜스크립트.txt`, `하이퍼프레임-영상-자막.srt`
- `tt-harness-kit/` (64KB, 9 파일 — 이식된 원본)

### M1 Max 메모리 (`~/.claude/projects/-Users-m1-max/memory/`)
- `project_tt_result_infra.md` — 4-티어 구조 맵
- `reference_tt_result_repo.md` — gh CLI 사용법
- `reference_vps_access.md` — VPS 접속 경로
- `feedback_credentials.md` — 비번 취급 원칙
- `feedback_sync_rule.md` — 커밋 후 동기화 규칙
- 기존 `project_memory_system.md` 업데이트 (v1.0 완료 상태 반영)

## 핵심 결정 사항

1. **ohmyclaw 이식 범위**: 9개 후보 중 `/tt-plan`, `/tt-work`, `/tt-review`, `/tt-ralph`, `/tt-consensus`, `/tt-doctor.sh` 6개만 — 모델 라우팅/계정 풀/브릿지는 제외
2. **VPS 배포 경로**: `/var/www/tt-result/` 는 git tracking 활성화, `/var/www/kettf/.../` 는 **대회 후 매핑 재정립 대기**
3. **보안 이슈 처리 타이밍**: 방화벽/권한 체크만 지금, 비번 교체는 대회 후 (다운타임 위험)
4. **동기화 규칙**: M1 Max 커밋 → GitHub push → m4sellma rsync 를 한 흐름으로
