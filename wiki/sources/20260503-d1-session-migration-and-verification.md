# 2026-05-03 D+1 세션 — Auth + 대회 생성 시스템 V1 + m4sellma 4 DB 마이그레이션

> 출처: 대화 (Windows PC Claude Code, F:\52nd_president_cup)
> 시점: 대회 종료 다음날 (D+1)
> 누적 commit: 17건 GitHub push 완료

---

## 핵심 흐름

### 1. P0-5 Auth 업그레이드 (D+1, D+2)
- §17 라이브러리 분석: Authlib(부채택) / pyotp(채택) / fastapi-users(부채택)
- 인증 모델 변경: OAuth-우선 → 비번 + 2FA(TOTP/Telegram)
  - Kakao 알림톡 OTP 도 §17 부채택 (사업자 등록 부담)
  - Telegram Bot API 직접 호출 (urllib, watchdog 패턴 재사용)
- 권한 매트릭스 4역할: superadmin / admin(league-scoped) / operator / viewer
- B-1: operator D+1 정의만, D+8~10 구현
- B-2: 별도 superadmin 백업 비번 (env-var 접근)
- D+2 백엔드: src/core/two_factor.py + src/routes/auth_2fa.py + 단위테스트 10/10

### 2. P0-10 대회 생성 시스템 V1
- 사용자 작성 규칙 (`docs/대회생성규칙-V1.md`) 정식 ingest
- 4유형 카테고리: 학년별 / 통합학년별 / 변형통합 / 통합(국선·왕중왕)
- 호프스 엑셀(2025) 분석: 8조 × 11명 예선 + 16명 본선 라운드로빈 + 예선상대 회피
- 사용자 결정 A안: 구조만, 단체전·시상 삭제, 랭킹 별도 메뉴
- Phase 1~4 완성:
  - Alembic 마이그레이션 c8e5d3a9f2b1 (tournament_info +tournament_type +category_config)
  - tournament_unified.html (3탭: 예선·본선·검증결과)
  - 라우터 분기 (tournament_type 기반)
  - 랭킹 별도 페이지 GET /rankings/{league_code}

### 3. 데이터 격리 (Step A 게이트 + Step B 마이그레이션)
- 발견: players/results 가 league_id 만으로 묶여 → 다른 대회 데이터 노출 위험
- Step A 게이트: `_is_isolated_tournament()` — unified 대회는 read API 빈 응답 + write 거부
- Step B1 마이그레이션 d4f7a2c8b3e6: players/results/tiebreak_results +tournament_id

### 4. Autopus-ADK 정합성 정렬
- university-league/autopus.yaml: language en→ko, lore.enabled true→false
- .claude/rules/autopus/*.md (10개) → .autopus-archive/ 로 이동 (gitignored)

### 5. m4sellma 직접 SSH (paramiko) + 4 DB 마이그레이션
- ftp.sellma.kr:822 m4sellma/240705 connection
- 발견: m4sellma 의 university-league + tt-result-src 모두 .git 없음 → paramiko sftp 로 sync
- 4 DB 일괄 마이그레이션 (alembic head=d4f7a2c8b3e6):
  - tt_result, kuttf_db, kettf_db, ktta_db
  - users +6 cols / tournament_info +2 cols / players|results|tiebreak_results +tournament_id
  - audit_log 테이블 + 인덱스
- 진행 중 metadata lock 발견 → 15개 hung MySQL connection kill 후 재시도 성공
- 백필 SKIP — `ORDER BY year DESC LIMIT 1` 가 호프스(2026) 잘못 매핑 위험
- UPDATE tt_result.tournament_info SET tournament_type='elementary_unified' WHERE id=3 (호프스)

### 6. A~D 검증 (8102/src/ 기준)
- A 메인 페이지 5개 카드 (랭킹 신규) ✓
- B 호프스(id=3) 3탭 unified (예선/본선/검증결과), 단체전·시상·랭킹 X, 학년 X ✓
- C 52회 회장기 5탭 그대로 + 랭킹 외부 링크 ✓
- D /rankings/{league} 3개 모두 200, ranking_page.html ✓

### 7. 외부 접근 미해결
- 외부 80 → 8100 (app/ OLD), 외부 8843 → 8101 (app/ OLD), 8102 (src/) forwarding 안 됨
- 부수: app/ 의 /api/ranking/* 가 kettf_tournament DB 하드코딩 → 500
- 다음 결정: 8100/8101 swap to src/ vs UniFi 라우터 8102 추가 vs app/ 미러링

---

## 누적 commit (오늘, GitHub `origin/main`)

```
00fcfb3 docs(talk): 2026-05-03 후반부 — m4sellma 4 DB 마이그레이션 + A~D 검증
f3d49cc docs(tournament): 유형 4 본선 규칙 명확화 — 예선 결과 본선 포함
256fc10 feat(model): 마이그레이션 적용 후 user/tournament_info 모델 활성화
861229e docs(talk): 2026-05-03 D+1 세션 기록 (이전 버전)
be6ac93 hotfix(model): user 6컬럼 비활성화 (마이그레이션 미적용 시 SELECT 깨짐 방지)
9e3cfb7 hotfix(model): tournament_info 모델 컬럼 비활성화
6cf9471 feat(tournament): 데이터 격리 — Step A 게이트 + Step B 마이그레이션
abd9de7 feat(tournament): 모델 활성화 + 랭킹 메뉴 별도 분리
c0b0422 feat(tournament): 유형 4 통합형 대회 화면 (Phase 1~3)
4ab6de1 docs(tournament): 대회 생성 규칙 V1 정식화 + 구현 계획
ac28246 chore(gitignore): .autopus-archive/
7d0079b chore(autopus): 정합성 정렬
97c3a75 docs(auth): 권한 매트릭스 — 사용자 결정 반영
41bdbec feat(auth): P0-5 D+1 산출물 내용 수정
3b85c9a feat(auth): P0-5 D+2 백엔드 구현
c5553e5 feat(auth): P0-5 D+1 라이브러리 분석 + 마이그레이션
d7f4010 chore(.gitignore): video/ 추가
fea243b chore: .gitmodules 복원
d2a6c60 chore: 작업트리 정리
```

---

## 다음 세션 우선순위

1. **외부 접근 fix** — 8100/8101 을 src/ 로 swap (사용자 결정 후)
2. **/api/ranking/* DB 매핑** — kettf_tournament 하드코딩 fix
3. **Step B2** — 모델·쿼리 격리 활성화 (tournament_id 필터)
4. **Auth D+3~D+14** — UI 템플릿, audit_log 조회, 역할 분리, VPS 배포
5. **8843 SSL 본문 잘림** — UniFi 라우터 사용자 작업
6. **m4sellma sync 메커니즘 정형화** — git clone 전환 검토
