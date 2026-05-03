# 2026-05-03 재연결 세션 — F.zip 압축 해제 + GitHub 동기화

**날짜**: 2026-05-03 오후  
**작업 위치**: m4sellma (192.168.1.202)  
**세션 유형**: 재연결 (D+1 세션 이후 컨텍스트 단절로 재시작)

---

## 세션 핵심 흐름

### 1. F.zip 압축 해제

**배경**: 이전 세션에서 M1 Max 개발 허브의 전체 데이터를 F.zip(15GB)으로 백업하여 `/Volumes/WorkSpace/claudespace/backup_data/F.zip`에 업로드 완료. 이번 세션에서 압축 해제 진행.

**문제 발생**: `unzip` 기본 명령 사용 시 한글 파일명(CP949 인코딩) 처리 실패. `write error (disk full?)` 오류(실제 디스크 여유 1.8TB). 1,920개만 추출 후 중단.

**해결**: Python `zipfile` 모듈 + CP949 디코딩으로 재처리.
```python
raw = info.filename.encode('cp437')
name = raw.decode('cp949')  # 한글 파일명 복원
```

**결과**: 64,556개 파일 추출 성공. 186개 오류(UTF-8/CP949 혼용 파일명 — docs/*.md 위주, 핵심 코드 정상). UTF-8 플래그(flag_bits & 0x800) 파일 14개 추가 복구.

**추출 위치**: `/Volumes/WorkSpace/claudespace/52nd_president_cup/`

**F.zip 주요 내용**:
- `52nd_president_cup/52nd_president_cup/`: 제52회 대통령배 초등 스코어시트 생성 도구 (VBA + Python + openpyxl)
- M1 Max 개발 허브 전체 데이터: tt-result 소스, DB 백업, 선수 등록 데이터, 대회 기록지 PDF
- llm-wiki-agent 독립 복사본 (raw 파일 포함)

### 2. GitHub pull — 26커밋 동기화

**배경**: m4sellma 로컬 레포가 GitHub보다 26커밋 뒤처진 상태(메모리에는 20커밋으로 기록돼 있었으나 실제 26개).

**인증**: GitHub PAT로 HTTPS 인증.

**pull 결과**: `e3e2021` → `042da2f` (44파일, 4,271 insertions)

**주요 변경 내역**:
| 영역 | 커밋 | 내용 |
|------|------|------|
| Auth | `3b85c9a`, `c5553e5` | PBKDF2 비번 해싱 + 2FA (TOTP/Telegram) 백엔드 (`src/core/two_factor.py`, `src/routes/auth_2fa.py`) |
| 대회 시스템 | `c0b0422` ~ `abd9de7` | 유형 4 통합형 화면 3탭 (`tournament_unified.html`), 랭킹 별도 메뉴 |
| 데이터 격리 | `6cf9471` | Step A 게이트 + Step B 마이그레이션 (`players/results +tournament_id`) |
| hotfix | `9e3cfb7`, `be6ac93` | tournament_info/user 모델 컬럼 비활성화 → 마이그레이션 적용 후 활성화 |
| Alembic | `6cf9471` 등 3개 | auth_upgrade_2fa, players_results_tournament_id, tournament_type_config |
| 랭킹 | `abd9de7` | `/rankings/{league_code}` 별도 페이지 + JS |

### 3. llm-wiki-agent 서브모듈 초기화

`git submodule update --init` 성공. `2db4a278` 체크아웃. raw 파일 마지막: `20260426_operating_model_and_venue_fix.md`.

---

## 현재 상태 (세션 종료 기준)

### 완료
- F.zip 압축 해제 완료 (`/Volumes/WorkSpace/claudespace/52nd_president_cup/`)
- GitHub main 브랜치 동기화 완료 (`042da2f`)
- llm-wiki-agent 서브모듈 초기화

### 미결 (§20 기준)
- Phase 5: 유형 4 예선→본선 알고리즘 구현 (`docs/plans/2026-05-03-tournament-creation-system.md`)
- Auth D+3: TOTP/Telegram 등록 UI, audit_log 조회
- VPS 배포: `git reset --hard origin/main` 동기화
- 8102 포트 UniFi 포워딩 미등록 (사용자 결정 대기)
- Step B2: 모델·쿼리 격리 활성화

---

## 핵심 교훈

1. **ZIP 한글 인코딩**: macOS `unzip`은 CP949 파일명을 처리 못함. Python `zipfile` + `encode('cp437').decode('cp949')` 패턴 필수.
2. **메모리 커밋 수 부정확**: 세션 메모리에 "20커밋 뒤처짐" 기록됐으나 실제 26개. 재연결 시 `git fetch` 후 실제 차이 확인 필수.
3. **llm-wiki-agent 서브모듈**: tt-result 레포 클론/초기화 시 `git submodule update --init` 별도 실행 필요 (자동 포함 안 됨).
