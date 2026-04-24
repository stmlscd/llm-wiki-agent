# Wiki Log

Append-only chronological record of all operations.

## [2026-04-24] ingest | mafia-codereview-harness 실물 CLAUDE.md 확인 후 §17 재평가

- **소스**: `raw/20260424_mafia_codereview_reassessment.md`
- **생성된 페이지**: `sources/20260424-mafia-codereview-reassessment.md`
- **업데이트**: memory/reference_mafia_codereview_harness.md (6항목 재평가표 + 실물 핵심 + 재검토 선행 작업 순서)
- **핵심**: 실물 CLAUDE.md 확인 → 안정성 리스크 중→낮음, 유지비용 중→중상. 결정 유지(대회 후 재검토) but 사유 재정의: "2 commit repo" → "yaml 선행 작성이 대회 시간 잠식"
- **모순**: 없음. 어제 결정의 근거만 정교화

## [2026-04-24] ingest | mafia-codereview-harness plugin 도입 전 분석 → 대회 후 재검토

- **소스**: `raw/20260424_mafia_codereview_deferred.md`
- **생성된 페이지**: `sources/20260424-mafia-codereview-deferred.md`
- **업데이트**: memory/reference_mafia_codereview_harness.md, docs/post-tournament-tasks.md P2-1
- **핵심**: §17 도입 전 분석 정책의 첫 실전 적용 — 6항목 분석 → 대회 D-4 리스크 고려 → 대회 종료(2026-05-02) 후 재검토 결정
- **모순**: 영상의 "AI 자율주행" 표현 vs 실제 (PR 리뷰 harness) — 마케팅/현실 차이로 해소

## [2026-04-24] ingest | 운영 워크플로우 §7 제정

- **소스**: `raw/20260424_operational_workflow.md`
- **생성된 페이지**: `sources/20260424-operational-workflow.md`
- **업데이트**: docs/server-rules.md §7, CLAUDE.md §16, memory feedback_server_rules.md
- **핵심**: VPS=정상화된 소스 운영, m4sellma=상시 백업+테스트, 문제는 m4sellma 재현→수정→VPS 이식 8단계 절차
- **모순**: 없음 (§1 서버 규칙을 절차로 확장)

Format: `## [YYYY-MM-DD] <operation> | <title>`

## [2026-04-24] ingest | TT-Result 브랜드 실배포 (VPS + m4sellma)

- **소스**: `raw/20260424_brand_rollout.md`
- **생성된 페이지**: `sources/20260424-brand-rollout.md`
- **변경된 파일**: `src/templates/index.html`, `src/static/css/main.css`, `university-league/app/templates/index.html`, `university-league/static/css/main.css`
- **핵심 사항**:
  - 어제 문서화한 브랜드 스펙 → 실 배포
  - 헤더(topbar + TT-Result 로고 + 탁구 네트 + TOURNAMENTS·PLAYERS·RESULTS·RANKINGS 네비) 추가
  - CSS 변수 prelude(다크 네이비 + 오렌지) 최상단 주입, 카드·모달·버튼 오버라이드
  - m4sellma(8100/8101/8102) + VPS(tt-result.sellma.kr) 양쪽 배포
  - 백업 `*.bak-20260424_140901` 생성 후 업로드 → 롤백 가능
  - 기존 alpine.js 로직 + 메뉴 클릭 동작 + 규칙 V1.1 준수 유지
- **검증**: HTTP 200, body 에 `brand-logo` + `class="tt"` 포함 확인, pytest 43/43
- **모순**: 없음

## [2026-04-23] ingest | m4sellma 보안 점검 대응 + UniFi 포워딩 실측

- **소스**: `raw/20260423_security_audit_response.md`
- **생성된 페이지**: `sources/20260423-security-audit-response.md`
- **업데이트된 페이지**: `docs/server-rules.md §6-a` (메인 repo), `docs/post-tournament-tasks.md` P0-8/P0-9 신규
- **핵심 사항**:
  - P0-1 MySQL 127.0.0.1 bind-address (my.cnf 신규)
  - P0-2 Ollama LaunchAgent (`com.sellma.ollama.plist`) + OLLAMA_HOST=127.0.0.1
  - P0-6 FastAPI docs APP_ENV=production 분기로 404
  - UniFi 포트포워딩 실측: m4sellma 공개 포트는 80/822/8843 만
  - WAN:2222 / WAN:8888 dead rules 사용자 GUI 삭제 완료
  - 8843 SSL EOF 는 기존 이슈, Let's Encrypt 로 대회 후 해결
- **모순**: 보안 점검 보고서 P0-1~P0-4 Critical 판정 vs 실측 (라우터 차단) — 정찰 범위 차이로 설명

## [2026-04-23] ingest | 서버 규칙 + 도입 전 분석 정책 제정

- **소스**: `raw/20260423_server_rules_and_adoption_policy.md`
- **생성된 페이지**: `sources/20260423-server-rules-and-adoption-policy.md`, `concepts/ServerRules.md`, `concepts/AdoptionPolicy.md`
- **핵심 사항**:
  - 서버 규칙: 운영=VPS, 백업/테스트=m4sellma, 상시 동일 데이터
  - 도입 전 분석 정책: Skill/MCP/Workflow 도입 전 6항목 분석 → 사용자 허가
  - 3곳 동기 저장: docs/ + CLAUDE.md §16·§17 + memory feedback
- **모순**: 없음

## [2026-04-23] ingest | m4sellma 핫 스탠바이 전환 (VPS 독립 복제)

- **소스**: `raw/20260423_m4sellma_hot_standby.md`
- **생성된 페이지**: `sources/20260423-m4sellma-hot-standby.md`
- **핵심 사항**:
  - m4sellma 에 로컬 MySQL 9.6 설치 + 4 DB 전체 복제
  - APP_SSH_TUNNEL=false 전환, 로컬 MySQL 사용
  - LaunchAgent 는 TCC(Full Disk Access) 이슈 → screen detached session 우회
  - 외부 공개 포트는 80·443·8843 만, 8100·8102 는 LAN 전용
- **모순**: 기존 `DeploymentArchitecture` 의 m4sellma 기술 갱신 필요 (다음 업데이트에서 반영)

## [2026-04-23] ingest | BlueKiwi WF:4 Step 6 FE 배선 완성 + 프로세스 리뷰

- **소스**: `raw/20260423_wf4_fe_wiring_completion.md`
- **생성된 페이지**: `sources/20260423-fe-wiring-completion.md`
- **핵심 사항**:
  - R1-01/R2-01/R3-01 FE 배선 완성 (gracket data-team, /my 페이지, autocomplete)
  - pytest 43/43 통과
  - WF:4 프로세스 리뷰: 인프라 85점 / 프로세스 60점 / 기능 완성 50→85점
- **모순**: 없음


Parse recent entries: `grep "^## \[" wiki/log.md | tail -10`

---

## [2026-04-14] install | Autopus-ADK v0.40.10

- Windows + Mac mini에 설치 완료
- university-league 프로젝트 초기화 (autopus.yaml)
- 활용: Lore(결정이력), arch(구조검증), orchestra(다중모델)

## [2026-04-14] ingest | 대통령기 점수 추가 및 랭킹 UI 업데이트

- **소스**: `raw/20260414_ranking_president_cup_add.md`
- **생성된 페이지**: `sources/20260414-ranking-president-cup-add.md`
- **업데이트된 페이지**: `concepts/PlayerRanking.md`, `wiki/index.md`, `wiki/overview.md`
- **핵심 사항**:
  - 대통령기(R2) rk_tournaments id=6 추가 → 6개 대회 체제
  - 5·6학년 남녀 147명 점수 입력, 전원 매칭
  - VPS index.html + main.js에 대통령기 컬럼 추가 (하드코딩 주의)
  - 여자6학년 학교명 3건 수정 (김태희·이소이→조동, 최라온→남평)
- **모순**: 없음

## [2026-04-14] ingest | 랭킹 선수 정보 수정 및 중복 합산

- **소스**: `raw/20260414_ranking_player_fixes.md`
- **생성된 페이지**: `sources/20260414-ranking-player-fixes.md`
- **업데이트된 페이지**: `concepts/PlayerRanking.md`, `wiki/index.md`, `wiki/overview.md`
- **핵심 사항**:
  - 학교명 수정 4건: 박해온·이하니·김나경(→포은), 오리온(→장량)
  - 이율 중복 합산: "이율"+"이 율"(공백) → 17위 75점
  - 황찬율 학년 간 합산: 4학년 종별10점 → 5학년 합산, 27위 65점
  - 선수 수 381→380명, 정합성 검증 불일치 0건
- **모순**: 없음

## [2026-04-13] ingest | 대학연맹 시상 + 마무리

- **소스**: `raw/20260413_university_awards_final.md`
- **생성된 페이지**: `sources/20260413-university-awards-final.md`
- **핵심**: 선발전→시상 변경, 상장대장(26-01~28) 자동 생성, 검사결과 128명 통과, 일정 5일, OCR 보조 확정
- **미완성**: 단체전 기록지(TEAM SCORE SHEET) — 내일 작업

## [2026-04-13] ingest | 대학연맹 완성 — 대진표 + 검사결과 + 일정

- **소스**: `raw/20260413_university_complete.md`
- **생성된 페이지**: `sources/20260413-university-complete.md`
- **핵심 사항**:
  - bracket_generator.py: 16/32/64강 대진표 자동 생성 + BYE 진출 + 빈 라운드 미리 생성
  - 대진번호 배정 UI: 팀/진출자 목록 → 번호 입력 → BYE 자동 채움
  - 기록지: DOUBLES/MIXED DOUBLES SCORE SHEET 자동 표기, BYE 제외
  - 검사결과: 개인전+단체전만 검사, 복식 묶음 제외, 김동한→김동환 오타 발견
  - 일정: 엑셀 → JSON → 테이블 표시 (5일, 4/28~5/1)
  - OCR: Claude Vision/PaddleOCR/Tesseract 모두 손글씨 한글 부정확 → 보조
  - 공방률: tiebreak.py 이식 완료
- **모순**: 없음

## [2026-04-13] ingest | 대학연맹 시스템 구축 + DB 분리 + 엑셀 업로드

- **소스**: `raw/20260413_university_league_build.md`
- **생성된 페이지**: `sources/20260413-university-league-build.md`
- **업데이트된 페이지**: `entities/UniversityLeague.md`, `concepts/DeploymentArchitecture.md`, `wiki/index.md`, `wiki/overview.md`
- **핵심 사항**:
  - DB 연맹별 분리: kettf_db(초등), kuttf_db(대학), ktta_db(협회), tt_result(시스템)
  - 52nd UI 이식 + API 형식 호환 (직접 배열 반환, 한글 키)
  - 대학연맹 7탭: 단체전/개인전/개인복식/혼합복식/선발전/일정/검사결과
  - 엑셀 파서: 7시트 자동 파싱 → kuttf_db 저장 (118명+231players)
  - 메인 메뉴 4개 확정: 대회등록/대회관리/대회기록확인/관리자등록
  - 모든 인쇄 세로(portrait) 전환
- **모순**: 없음

## [2026-04-13] ingest | 랭킹 시도 수정 및 버튼 삭제, 최종 백업

- **소스**: `raw/20260413_ranking_province_fix_final.md`
- **생성된 페이지**:
  - `sources/20260413-ranking-province-fix-final.md` — 시도 수정 + 버튼 삭제 + 최종 백업 소스 페이지
- **업데이트된 페이지**:
  - `concepts/PlayerRanking.md` — 시도 데이터 정리, UI 정리 섹션 추가
  - `wiki/overview.md` — 시도 수정 및 최종 마무리 기재
  - `wiki/index.md` — 소스 항목 추가
- **핵심 사항**:
  - rk_players province 45건 수정 (빈값/기타/오류 → 17개 시도 정상화)
  - 랭킹 메뉴 '회장기 점수 산출'/'엑셀 내보내기' 버튼 삭제
  - VPS 최종완성 백업: app_20260413_최종완성.tar.gz (7.1MB) + SQL dump (322KB)
  - m4sellma 동기화 616파일
- **모순**: 없음

## [2026-04-12] ingest | 회장기 랭킹 포인트 산출 및 DB 업데이트

- **소스**: `raw/20260412_ranking_hoejanggi_update.md`
- **생성된 페이지**:
  - `sources/20260412-ranking-hoejanggi-update.md` — 회장기 포인트 산출 소스 페이지
- **업데이트된 페이지**:
  - `concepts/PlayerRanking.md` — rk_* 행 수 갱신 (343→381명, 773→1064건), 회장기 포인트 테이블 및 배치 판정 로직, 접속 이슈 추가
  - `entities/52ndPresidentCup.md` — 대회 최종 결과(우승/준우승) 추가, PlayerRanking 연결
  - `wiki/overview.md` — PlayerRanking 데이터 수치 갱신, 회장기 반영 완료 기재
  - `wiki/index.md` — 소스 항목 추가
- **핵심 사항**:
  - 4·5·6학년 남녀 291명 회장기 R1 포인트 산출 → VPS MySQL rk_scores INSERT
  - 신규 선수 39명 rk_players 등록 (342→381명)
  - rk_rankings dense ranking 재계산 (382건)
  - 엑셀 보고서 2개 생성: 52회_회장기_랭킹_남자.xlsx, 52회_회장기_랭킹_여자.xlsx
  - kettf_admin 계정 Access denied → root 계정 우회, 3306 미개방 → paramiko SSH 경유
- **모순**: rk_players 기존 위키 343명 vs 실제 342명 (1명 차이) — 업데이트로 해소

## [2026-04-12] backup | VPS 소스 + MySQL DB 백업 → Mac mini 저장

- **VPS 소스 백업**: `vps_52nd_20260412.tar.gz` (2.8MB) — /var/www/kettf/2026/52nd/ 전체
- **MySQL 덤프**: `kettf_tournament_20260412.sql` (316KB) — kettf_tournament DB 전체
- **저장 위치**: Mac mini `/Volumes/WorkSpace/Project_src/claudespace/52nd_president_cup/backup/20260412/`
- **포함 DB**: tournament.db, ranking.db, tournament_local.db, 52nd.db (SQLite) + MySQL dump

## [2026-04-12] ingest | FastAPI 마이그레이션 착수 — 계획 수립 + 인프라 준비

- **소스**: `raw/20260412_fastapi_migration_kickoff.md`
- **생성된 페이지**:
  - `sources/20260412-fastapi-migration-kickoff.md` — 마이그레이션 착수 소스 페이지
- **업데이트된 페이지**:
  - `entities/UniversityLeague.md` — 착수 상태, 실행계획 타임라인, 네트워크 구성 추가
  - `entities/Sellma.md` — www.sellma.kr Mac mini 테스트 서버 추가
  - `concepts/DeploymentArchitecture.md` — Mac mini 테스트 환경 + VPS tt-result 예정 추가
  - `wiki/index.md` — 소스 항목 추가
  - `wiki/overview.md` — 다음 프로젝트 "착수 완료" 업데이트
- **핵심 사항**:
  - Mac 현장 작업물 → Windows 역동기화 (paramiko SFTP)
  - Phase 0~9 총 21일 실행계획 수립
  - ORM 11모델 + Alembic 초기 마이그레이션 완료
  - www.sellma.kr 외부 접속 확인 (UniFi 80/8843 → Mac:8100)
  - 52nd 최종 스냅샷 app_20260412_final.tar.gz (3.7MB)
- **모순**: 없음

## [2026-04-11] query | 탁구경기결과관리시스템 업데이트 계획

## [2026-04-11] ingest | 단체전 기록지 교보생명컵 기준 수정 & 16강 game_format 버그 수정

- **소스**: `raw/20260411_scoresheet_fix.md`
- **생성된 페이지**:
  - `sources/20260411-scoresheet-fix.md` — 기록지 수정 + game_format 버그 수정 소스 페이지
- **업데이트된 페이지**:
  - `concepts/ScoreSheet.md` — 단체전 기록지 교보생명컵 기준 확정 구조 (5 sub-row, 높이표, tbody 단일화)
  - `concepts/TeamMatch.md` — game_format 기본값 버그 및 SQL 수정 내용 추가
  - `concepts/FieldOperationLessons.md` — 교훈 9 추가 (tbody 분리 시 브라우저 렌더링 깨짐)
  - `wiki/index.md` — 소스 항목 추가
  - `wiki/overview.md` — ScoreSheet 단체전 구조 확정 반영
- **핵심 발견**:
  - 단체전 기록지 구조 확정: 경기당 5 sub-row × 5.5mm, Adviser/Winner/Signature 단일 tbody
  - 선수명 + 승수박스: colspan=5 rowspan=5 단일 셀
  - 16강 자동 생성 매치 game_format 기본값 오류 → 명시적 UPDATE 필요
  - tbody 분리 시 브라우저가 Winner/Signature를 오른쪽으로 분리 렌더링 — CSS border 사용 권장

## [2026-04-11] ingest | 백업 복원 & 시상 탭 서브탭 정리

- **소스**: `raw/20260411_backup_restore_awards_fix.md`
- **생성된 페이지**:
  - `sources/20260411-backup-restore-awards-fix.md` — 백업 복원 + 시상 탭 정리 소스 페이지
- **업데이트된 페이지**:
  - `concepts/FieldOperationLessons.md` — 교훈 7(백업 복원), 교훈 8(sessionStorage 방식) 추가, 수정완성본 백업 패턴
  - `concepts/DeploymentArchitecture.md` — 수정완성본 백업 패턴 및 명명 규칙 추가
  - `wiki/index.md` — 소스 항목 추가
  - `wiki/overview.md` — 4/11 교훈 및 시상 탭 변경 반영
- **핵심 발견**:
  - 코드 손상 시 디버깅보다 VPS tar.gz 복원이 빠름 (app_20260410_154730.tar.gz 복원)
  - sessionStorage + location.reload() 방식이 SPA 직접 호출보다 현장에서 안정적
  - 시상 탭: 남자단체/여자단체 제거, 검사결과 기본 탭 설정
  - 수정완성본 백업: `app_20260411_152404_수정완성본.tar.gz` 생성

## [2026-04-11] ingest | 브래킷 버그 수정 & ngtos_tt DB 분석

- **소스**: `raw/20260411_bracket_fix_and_ngtos.md`
- **생성된 페이지**:
  - `sources/20260411-bracket-fix-ngtos.md` — 브래킷 버그 수정 + ngtos_tt 분석 소스 페이지
- **업데이트된 페이지**:
  - `concepts/TournamentBracket.md` — 오른쪽 절반 인덱스 공식 버그 수정 내용 + Flask 템플릿 경로 주의 추가
  - `concepts/DeploymentArchitecture.md` — Flask 템플릿 이중 경로 혼동, VPS scp 별도 배포 필요성, Windows 동기화 덮어쓰기 위험 추가
  - `wiki/index.md` — 소스 항목 추가
  - `wiki/overview.md` — TournamentBracket 인덱스 공식, DeploymentArchitecture VPS/템플릿 경로 주의 반영
- **핵심 발견**:
  - `print_bracket.html` 오른쪽 결과 미표시 버그: `half // (2 ** r)` → `half // (2 ** (r + 1))` 수정
  - Flask 실사용 템플릿 경로: `dist/templates/`, `web/templates/` (중첩 경로는 미사용)
  - VPS 배포는 m4sellma rsync와 독립적 — 별도 scp 필요
  - ngtos_tt(KTTA 공식 DB): 188개 테이블, 7개 도메인, 대학연맹 시스템 설계 참조용
- **모순**: DeploymentArchitecture의 "배포 시 scp" 설명이 VPS 자동 배포를 암시하는 인상을 줄 수 있었으나, VPS 수동 scp 필수임을 명시적으로 보강함.

## [2026-04-10] ingest | 전략 분석 및 다음 단계 계획 (52회 최종)

- **소스**: `raw/20260410_strategy_and_next_phase.md`
- **생성된 페이지**:
  - `sources/20260410-strategy-next-phase.md` — 전략 분석 소스 페이지
  - `concepts/TechStackMigration.md` — Flask→FastAPI, Alpine.js 전환 개념
  - `concepts/FieldOperationLessons.md` — 현장 6대 반복 패턴 및 교훈
  - `entities/UniversityLeague.md` — 다음 프로젝트 엔티티
  - `entities/Gnuboard6.md` — FastAPI 방향 외부 검증 엔티티
- **업데이트된 페이지**: `wiki/index.md`, `wiki/overview.md`
- **모순**: 없음. 기존 "미해결/확장 예정" 항목이 대학연맹으로 이관 처리됨.

## [2026-04-10] ingest | 랭킹 API MySQL 마이그레이션

- **소스**: `raw/20260410_ranking_mysql.md`
- **생성된 페이지**:
  - `sources/20260410-ranking-mysql.md` — 랭킹 API MySQL 마이그레이션 소스 페이지
  - `concepts/PlayerRanking.md` — 선수 랭킹 시스템 개념 페이지
- **업데이트된 페이지**: `wiki/index.md`, `wiki/overview.md`
- **모순**: 없음. overview.md "랭킹 시스템 보강(미해결)" 항목을 완료 처리 반영.

## [2026-04-10] ingest | 대진표/기록지 출력 표시 개선 작업

- **소스**: `raw/20260410_print_display.md`
- **생성된 페이지**:
  - `sources/20260410-print-display.md` — 출력 표시 개선 소스 페이지
  - `concepts/PrintDisplay.md` — 인쇄 표시 개선 개념 페이지
- **업데이트된 페이지**: `wiki/index.md`, `wiki/overview.md`
- **모순**: 없음

## [2026-04-10] ingest | 제52회 회장기 전국 초등학교 탁구대회 운영 시스템

- **소스**: `52nd_president_cup/` 프로젝트 전체 (docs/, skills/, web/, dist/, ranking_2025/ 등)
- **생성된 페이지**:
  - `sources/52nd-president-cup.md` — 메인 소스 페이지
  - `entities/KETTF.md` — 한국초등탁구연맹
  - `entities/52ndPresidentCup.md` — 제52회 회장기 대회
  - `entities/Sellma.md` — sellma.kr 플랫폼 및 m4sellma 허브
  - `concepts/RoundRobinLeague.md` — 조별 리그전 알고리즘
  - `concepts/TieBreakRanking.md` — 공방율 동률 처리 (ITTF)
  - `concepts/TournamentBracket.md` — 본선 대진표 및 시드 배치
  - `concepts/TeamMatch.md` — 학교 대항 단체전
  - `concepts/ScoreSheet.md` — KTT 표준 A4 기록지
  - `concepts/DeploymentArchitecture.md` — 3중 배포 환경
  - `concepts/PlayerVerification.md` — 활동제한 선수 검증
- **모순**: 없음 (최초 인제스트)

## [2026-04-14] ingest | 단체전 세트 점수 오류 수정 및 검증 로직 추가

- **소스**: `sources/20260414-team-score-validation.md`
- **주요 내용**: 단체전 세트 스코어 2:0→3:0 데이터 수정, 탁구 점수 유효성 검증(11점제/듀스/오타) 프론트엔드+서버 이중 추가
- **업데이트**: `overview.md` TeamMatch 섹션, `index.md` 소스 추가
- **모순**: 없음

## [2026-04-14] ingest | tt-result 디자인 문서 확정

- **소스**: `sources/20260414-tt-result-design.md`
- **주요 내용**: 탁구경기결과관리시스템 Approach C(단계적 릴리스) 승인. 대학연맹 4/28~5/2 첫 실전(D-14=오늘). FastAPI+Alpine.js 모듈러 아키텍처. 독립 리뷰어 13이슈 중 11수정.
- **업데이트**: `overview.md` 다음 프로젝트 섹션 전면 갱신, `index.md` 소스 추가
- **모순**: 없음


## [2026-04-16] ingest | MindVault 훅 Windows 깜빡임 이슈 해결

- **작업**: FieldOperationLessons 컨셉 페이지에 교훈 #10 추가
- **주요 내용**: PostToolUse 훅(lore-hook, rules-hook)이 Windows에서 Bash/Edit/Write마다 콘솔 창 깜빡임 유발. PostToolUse 훅 제거, UserPromptSubmit 훅만 유지로 해결.
- **원칙**: Windows에서 빈번한 PostToolUse 훅은 피하고, 꼭 필요한 훅만 UserPromptSubmit에 배치

## [2026-04-16] ingest | MindVault 훅 정리 + 단체전 점수 수정 + 서버 검증

- **소스**: `sources/20260416-mindvault-hook-score-fix.md`
- **주요 내용**: Windows PostToolUse 훅 제거(깜빡임), 여자32강 11:12→11:13 수정(kettf_tournament DB), 탁구 규칙 검증 VPS 배포, llm-wiki-agent fork(stmlscd)
- **업데이트**: `overview.md` TeamMatch DB 주의사항, `index.md` 소스·컨셉 추가, `FieldOperationLessons.md` 교훈#10
- **모순**: 없음

## [2026-04-16] ingest | tt-result.sellma.kr 배포 및 시스템 통합 전체 기록

- **소스**: `sources/20260416-tt-result-deployment.md`
- **주요 내용**: VPS 신규 배포(Apache+uvicorn+SSL), DB 이식(초등 전체+대학 선택적), 랭킹 6대회 통합+통합순위, 개인전 수정 모달(양 연맹), 기록지 버튼 복원, 관리자 기능(순위계산/기권/이동), 대학연맹 기권 취소선 방식, 탁구 점수 검증, llm-wiki-agent fork
- **업데이트**: `overview.md` 인프라 4중 환경+Phase 1 완료, `index.md` 소스 추가
- **모순**: 없음

## [2026-04-17] ingest | 대학연맹 테스트 경기 결과 삭제

- **작업**: kuttf_db tournament_matches에서 테스트용 경기 결과 초기화
- **주요 내용**: 복식_남/복식_여/혼합복식 토너먼트 점수 전체 초기화, 32강 미완료 경기의 승자가 16강에 자동 진출한 데이터도 제거. BYE 자동 진출만 유지.
- **모순**: 없음

## [2026-04-17] ingest | 대한탁구협회 경기결과 전송 시스템 영상 분석

- **소스**: `sources/20260417-ktta-transmission-analysis.md`
- **주요 내용**: 영상 3개(23.5분) 분석. KVPN→VPN(PPTP)→대회운영프로그램(tos09)→result.sports.or.kr 6단계 절차. 전송 테이블 20개(nsl*/nst*/nsd*). 대회코드 202614448. 로컬DB 직접삽입 자동화 전략.
- **모순**: 없음

## [2026-04-17] ingest | KTTA 자동 전송 계획 + VPN 접속 테스트 결과

- **소스**: `sources/20260417-ktta-auto-transfer-plan.md`
- **주요 내용**: VPN 연결 후 원격DB 포트 OPEN 확인(계정 미확인), 로컬DB root/password 확인, 로컬DB 삽입 → 프로그램 전송 방식 4단계 계획. 이전 3가지 실패 회피 전략.
- **모순**: 없음

## [2026-04-17] ingest | KTTA 원격 DB 접속 성공 + Phase 1-2 완료

- **핵심 발견**: ngtos_tt.exe 프로세스 메모리에서 원격 DB 인증정보 추출
  - 서버: 182.162.101.245:13834
  - 계정: jinjin / jinjin2024
  - DB: ngtos_tt (MariaDB 11.4.2)
- **Phase 1 완료**: ktta_sync_v2.py 매핑 스크립트 작성, 11개 테이블 자동 변환
- **Phase 2 완료**: 로컬 DB 삽입 성공 (총 3,226건)
- **로컬 DB**: root / password (127.0.0.1:3307)
- **전략 전환**: 로컬 삽입→프로그램 전송 방식에서, 원격 DB 직접 삽입 방식으로 변경 가능
- **Phase 3**: VPN 연결 후 원격 DB 직접 삽입 테스트 (5/3 실시)

## [2026-04-17] query | Codex 코드 리뷰 — 호환 라우터 보안 및 대진표 문제

- **일반 리뷰**: P1 인증 누락(5개 POST), P1 64강 미지원, P2 경기 사유 미보존
- **Adversarial 리뷰**: HIGH 서브모듈 dirty 상태, MEDIUM 플러그인 강제 활성화
- **수정 완료**: 5개 전체 이슈 수정 적용 (`_new_routes.txt`, `.claude/settings.json`, 서브모듈 커밋)
- **GitHub**: stmlscd/tt-result#1 이슈 + 코멘트 생성
- **위키 저장**: `syntheses/20260417-codex-review-compat-router.md`

## [2026-04-18] ingest | KTTA 공식 웹사이트 분석 (ktt.or.kr / koreatabletennis.com)

- **분석 대상**: ktt.or.kr (종별선수권), koreatabletennis.com (종합선수권)
- **로그인**: admin / vm0701, 심판 모드, 대회코드+테이블번호 선택
- **핵심 발견**:
  - 동일 코드베이스(PHP 그누보드 theme06), 대회별 분리 운영
  - 점수 입력: `pcs_getscoredata.php` AJAX POST, wr_1~wr_14 (7세트×2선수)
  - 실시간 갱신: `pcs_getgamedata.php` 60초 폴링, t_status(0=준비/1=경기중/2=종료)
  - 대진표: `win.tournamentData` JS 직접 임베드 + jquery.gracket.js 렌더링
  - 기록지 인쇄: `printtable.php`, `printtable_direct.php`
- **모순**: 없음

## [2026-04-18] ingest | tt-result 시스템 적용 규칙 V1.0 (Priority 0)

- **원본**: `docs/탁구경기결과관리시스템-규칙-V1.0.md`
- **위키**: `concepts/TTResultRules.md`
- **7개 규칙**: 엑셀업로드, 예선→본선, 공방률, 선발전리그, 단체전유연성, 출력규격, 기권취소선
- **코드 검증 결과**: 4개 구현됨, 3개 미구현 (16인 리그, 5단식, 256강 인쇄)
- CLAUDE.md에 최우선 규칙 섹션 추가

## [2026-04-18] fix | 대진표 어그러짐 수정 및 64강 한 장 출력

- **문제 1**: gracket-lite.js 복식 이름 2줄로 Canvas 연결선 어그러짐
- **해결**: 1라운드 전체 게임 최대 높이로 통일 (`_normalizeGameHeights`)
- **문제 2**: 혼합복식 64강 대진표가 32강×2장으로 분할
- **해결**: print_routes.py에서 분할 코드 제거, 한 장 출력 (A4 가로)
- **문제 3**: 후속 라운드 빈 슬롯이 표시 안 됨
- **해결**: compat_52nd.py에서 빈 매치 플레이스홀더 자동 생성
- **DB 수정**: 대회명에서 "및 2026 한.일 대학탁구대회 선발전" 삭제
- VPS 직접 배포 완료

## [2026-04-19] fix | QA 검증 — 보안/인증 수정 (Health 52→72)

- **QA 대상**: https://tt-result.sellma.kr (Full, curl+WebFetch)
- **발견**: 7개 이슈 (CRITICAL 1, HIGH 3, MEDIUM 2, LOW 1)
- **수정 완료**:
  - ISSUE-001 CRITICAL: 8개 POST API에 `require_login` 인증 추가
  - ISSUE-002 HIGH: admin 비밀번호 해시 재생성 (vm0701)
- **오탐 확인**: ISSUE-003(세션 필요), ISSUE-005(URL 오류)
- **다음 검증으로 이관**: ISSUE-004(랭킹 404), ISSUE-006(인쇄 일부), ISSUE-007(메인 UX)
- VPS + m4sellma 동기화 완료

## [2026-04-19] ingest | 초등연맹 랭킹 선수 수정 2건

- **작업**: kettf_tournament.rk_players/rk_scores 수정
- **수정 1**: 이효은(여자5학년, id=141) 소속 삼정 → 화성도시공사
- **수정 2**: 김한서(여자6학년, id=225) 교보 15점 추가, 총점 70→85점
- **모순**: 없음

## [2026-04-20] ingest | tt-harness-kit + 대학연맹 현장 대응 + 규칙 V1.1

- **tt-harness-kit**: 5개 슬래시 명령(tt-plan/work/review/ralph/consensus) + tt-doctor.sh 설치
- **tt-plan**: 대학연맹 대회 현장 대응 4태스크 계획 수립
- **tt-review**: 인증 누락 3건(forfeit/move-player/singles-forfeit) CRITICAL 수정, VPS 적용
- **규칙 V1.1**: 6~9인 매치 오더(7인=21경기~9인=36경기) + 기록지 출력 셀 규격
- **현장 체크리스트**: VPS 긴급수정, DB 백업, 대체 서버, 종목별 운영 순서

## [2026-04-20] ingest | tt-harness-kit 배포 + VPS 코드 매핑 불일치 발견 (M1 Max 세션)

- **소스**: `raw/20260420_tt_harness_kit_session.md`
- **생성된 페이지**: `sources/20260420-tt-harness-kit-deployment.md`
- **작업자**: 상철 + Claude (M1 Max)
- **이식 대상**: ohmyclaw (MIT) → tt-harness-kit (6 커맨드 + 2 스크립트)
- **배포 범위**: 윈컴(SMB) + M1 Max(/tmp/ttr-clone) + m4sellma(rsync) + VPS(/var/www/tt-result/)
- **발견**: VPS `/var/www/kettf/2026/52nd/` ↔ github `52nd_president_cup/web/` **코드베이스 독립 진화**
  - wsgi.py 변수명 불일치 (`app` vs `application`), app.py 완전히 다른 기능
  - 실 배포 시 gunicorn `App failed to load` → 자동 롤백 성공 (다운타임 5초)
  - 대회 후 P1-1 매핑 재정립 필요 (`docs/post-tournament-tasks.md`)
- **보안 이슈**: MySQL 자격증명 + VPS root 비번이 저장소 평문 (P0, 대회 후 즉시 교체)
- **신규 원칙**: commit → github push → m4sellma rsync 를 한 흐름으로 (feedback memory 저장)

## [2026-04-20] ingest | 3~20인 매치 오더 엑셀 검증 + 규칙 V1.1

- **매치 오더**: 초등연맹 오더지 엑셀 18개(3~20인) 비교 분석, 5~9인 완전 일치, 10~20인 추가
- **match_orders.py**: 별도 분리 (300행 제한), 전수 검증 OK
- **규칙 V1.1**: 기권=취소선 필수, 6~9인 매치 오더, 기록지 셀 규격 추가
- **tt-review**: 인증 누락 3건 CRITICAL 수정 (forfeit/move-player/singles-forfeit)
- **tt-plan**: 대학연맹 현장 대응 4태스크 계획, 체크리스트 작성
- **환경 점검**: tt-doctor 10/10 PASS

## [2026-04-20] ingest | src/ 구조 마이그레이션 완료
- university-league/app/ → src/ 전면 이전
- compat_52nd.py(1074행) → 5개 라우터 분리
- 44 Python, 59 endpoints, m4sellma 테스트 OK

## [2026-04-22] ingest | 초등연맹 랭킹 DB 수정
- 소속변경: 김하늘(140), 김지온(147) → 경기/화성도시공사
- 점수이동: 김지훈 4학년(304) 삭제 → 5학년(40) 종별+10 (total 70)
- 점수추가: 배선우(350) 종별+10 (total 25)
- 적용: kettf_tournament (VPS), m4sellma는 터널로 동일 DB

## [2026-04-22] ingest | 구글 캘린더 등록 및 KTTA 전문 대회 일정 분석

- **소스**: `raw/20260422_calendar_ktta_schedule.md`
- **생성된 페이지**: `sources/20260422-calendar-ktta-schedule.md`
- KTTA 2026 대회일정 PDF 분석 → 전문 대회 확정 21건 ICS 생성 → 구글 캘린더 등록
- 병원 진료 4건 (6/2, 7/21, 7/23, 9/3) ICS 등록
- talk/2026_04_22_talk.md 대화 기록 저장
- 기억 DB: reference_ktta_2026_schedule.md 추가

## [2026-04-22] ingest | Claude Code 외부 스킬 평가

- **소스**: `raw/20260422_skill_evaluation.md`
- **생성된 페이지**: `sources/20260422-skill-evaluation.md`
- peon-ping, ok-skills, caveman, skill.color-expert 4개 평가
- 결론: 현재 불필요, color-expert만 UX 단계에서 재검토

## [2026-04-22] ingest | MindVault 훅 수정 + Windows 터미널 선택

- **소스**: `raw/20260422_terminal_mindvault_fix.md`
- **생성된 페이지**: `sources/20260422-terminal-mindvault-fix.md`
- MindVault 훅 CREATE_NO_WINDOW 깜빡임 해결
- Alacritty 포터블 설치 (Windows용 iTerm2+tmux 대안)
- MCP 서버 미설정 확인

## [2026-04-22] ingest | BlueKiwi MCP 서버 설치 및 연결

- **소스**: `raw/20260422_bluekiwi_mcp_setup.md`
- **생성된 페이지**: `sources/20260422-bluekiwi-mcp-setup.md`
- BlueKiwi v1.2.3 설치, 프로필 tt-result
- Claude Code + Claude Desktop MCP 연결 완료
- 8개 런타임 연동
