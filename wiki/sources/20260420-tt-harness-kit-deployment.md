# 20260420 tt-harness-kit 배포 + VPS 코드 매핑 불일치 발견

> **소스**: `raw/20260420_tt_harness_kit_session.md`
> **작업 시각**: 2026-04-20 09:30~10:55 KST (M1 Max 세션)
> **관련 문서**: `tt-result/docs/post-tournament-tasks.md`, `tt-result/docs/deployment-map.md`, `tt-result/docs/security-audit-2026-04-20.md`

## 한 줄 요약

ohmyclaw(OpenClaw 하네스 스킬, MIT) 의 핵심 5개 개념을 tt-result 스택에 맞춘 `tt-harness-kit` 으로 이식하여 윈컴/M1 Max/m4sellma/VPS 4대에 배포. VPS kettf 실 배포 시 **github↔VPS 코드베이스 독립 진화** 로 인한 gunicorn 장애 발생, 자동 롤백으로 5초 내 복구, 대회 후 P1-1 정식 정리 대기 상태.

## tt-harness-kit 구성 (6 커맨드 + 2 스크립트 + 1 문서)

| 파일 | 출처 (ohmyclaw) | 역할 |
|------|----------------|------|
| `.claude/commands/tt-plan.md` | `prompts/planner.md` | 계획 수립 (`docs/plans/YYYY-MM-DD-<slug>.md` 저장) |
| `.claude/commands/tt-work.md` | `prompts/executor.md` | 자율 실행 + Lore 커밋 포맷 |
| `.claude/commands/tt-review.md` | `prompts/reviewer.md` | 5관점 리뷰 + 갭 감지 5유형 |
| `.claude/commands/tt-ralph.md` | ralph 동사 (oh-my-claudecode 유래) | executor+verifier 끝까지 루프 (최대 5회) |
| `.claude/commands/tt-consensus.md` | `plan --consensus` | planner→architect→critic 3단 합의 |
| `scripts/tt-doctor.sh` | `scripts/doctor.sh` | 10항목 환경 점검 |
| `scripts/deploy-kettf.sh` | 신규 | kettf 배포 자동화 (rsync + systemd reload) |
| `52nd_president_cup/web/.env.example` | 신규 | kettf 환경변수 템플릿 |
| `docs/gap-detection-patterns.md` | 신규 (이식 가이드) | 5유형 갭 감지 + tt-result 예시 |

## 5유형 갭 감지 (이번에 포팅된 핵심 개념)

tt-review 커맨드가 **항상** Stage 5 로 실행하는 드리프트 방지 로직:

| 유형 | 정의 | tt-result 예시 |
|------|------|---------------|
| `assumption_injection` | 사용자가 말하지 않은 가정 주입 | "점수 수정 API" 요청에 JWT 인증 임의 추가 |
| `scope_creep` | 요청하지 않은 기능/복잡도 | "순위 API" 요청에 캐시 레이어 추가 |
| `direction_drift` | 전체 방향이 의도와 다름 | FastAPI 요청에 Django로 재작성 제안 |
| `missing_core` | 핵심 기능 누락 | "대회 생성 API" 인데 참가자 등록 빠짐 |
| `over_engineering` | 과도한 추상화 | 단순 CRUD에 Repository 3단 + Unit of Work |

## VPS 인프라 지도

4-티어 구조:

```
윈컴 (amd_sellma02, 192.168.1.246) — 주 개발
  │ git push
  ▼
GitHub stmlscd/tt-result — source of truth
  │
  ├── m4sellma (192.168.1.179) — 백업/테스트
  ├── M1 Max — 허브/설계
  └── VPS (115.68.177.35) — 프로덕션
      ├── /var/www/tt-result/    (FastAPI, port 8100, git tracking ✅)
      └── /var/www/kettf/2026/52nd/ (Flask, port 8000, 대회 운영 중)  ★
```

## VPS 실 배포 장애 상세 (2026-04-20 10:51 KST)

### 현상
- `deploy-kettf.sh` 실 실행 → `systemctl reload-or-restart kettf-tournament.service`
- gunicorn worker `exit code 4, Reason: App failed to load`
- 자동 롤백 (백업 경로 `/var/www/kettf/2026/52nd.backup-20260420-105134` → 원복)
- HTTP 200 정상 복구, 다운타임 ~5초

### 원인: github ↔ VPS 코드베이스 독립 진화

| 파일 | VPS 프로덕션 | github `52nd_president_cup/web/` |
|------|-------------|----------------------------------|
| `wsgi.py` | `from app import app` | `from app import app as application` |
| `app.py` | `ADMIN_ID='admin'`, `login_required` 데코레이터 | `RANKING_DB_PATH`, `get_ranking_db()`, sqlite3 임포트 |
| 기능 | admin 로그인 + 세션 관리 | sqlite ranking DB 연동 |

→ gunicorn은 `wsgi:app` 으로 실행하는데 github 버전에선 변수명이 `application` 으로 바뀜 → 로드 실패.

### 교훈
- `docs/deployment-map.md` 의 매핑 전제 (github `52nd_president_cup/web/` → VPS `/var/www/kettf/`) 가 **틀림**
- 두 코드베이스는 완전히 독립적으로 진화한 상태
- 대회 기간엔 VPS 에서 직접 편집 유지, 대회 후 P1-1 에서 정식 정리

## 보안 감사 핵심 (대회 후 즉시 조치 필요)

### 🚨 CRITICAL (P0)
1. **MySQL `sellma0080` 비밀번호** 가 `52nd_president_cup/web/db.py` 에 **평문 하드코딩**
   - 평문: `Ccoals4096#%#%` (github 히스토리에 영구 기록됨)
   - 조치: 대회 종료 즉시 비번 교체 + `db.py` → `.env` 리팩토링 + BFG로 history 정리
2. **VPS root 비밀번호** 가 `docs/서버.md` 에 **평문**
   - 평문: `P@0SY5C^`
   - 조치: 교체 + docs 에서 제거

### 이번 세션에서 처리한 완화 조치
- `52nd_president_cup/web/.env.example` 템플릿 추가 (리팩토링 시 참조)
- `docs/security-audit-2026-04-20.md` 감사 보고서 + 체크리스트 6개 항목

## 배포 스크립트 안전 기능 (실전 검증됨)

### `deploy-kettf.sh` 의 안전장치
1. **환경변수 override** (테스트용)
   ```
   KETTF_REPO / KETTF_DST / KETTF_SERVICE / SKIP_SYSTEMD / SKIP_GIT_PULL
   ```
2. **exclude 목록** (런타임/운영 자산 보호)
   - `*.db`, `__pycache__/`, `backup*/`, `venv/`, `.env`, `*.log`
   - `player_register_*/` (선수 등록 CSV)
   - `migrate_*.py`, `check_integrity.py`
   - `templates/admin_users.html`
3. **배포 전 자동 백업** `.backup-YYYYMMDD-HHMMSS`
4. **health check** (HTTP 200 확인) + 실패 시 롤백 명령 출력
5. **dry-run 모드** (rsync `--dry-run` + systemctl 호출 생략)

## 운영 원칙 (상철님 세션 중 표명)

1. **"m4sellma와 github에는 항상 동기화"** — commit → push → rsync 를 단일 흐름으로
2. **대회 중 파괴적 변경 금지** — 서비스 다운 위험은 대회 후
3. **비번은 세션 내에서만** — 메모리 파일 저장 금지

## 참고 링크

- ohmyclaw 원본: https://github.com/jkf87/ohmyclaw
- openclaw 한국어 문서: https://jkf87.github.io/openclaw-docs-ko/
- Hyperframe (관련 영상 소개): Pagen 사 AI 에이전트용 비디오 생성 도구

## 태그

`harness`, `deploy`, `security`, `infrastructure`, `ohmyclaw`, `kettf`, `post-tournament`
