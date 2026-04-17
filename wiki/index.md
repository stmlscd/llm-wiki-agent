# Wiki Index

This file is maintained by the LLM. Updated on every ingest.

## Overview
- [Overview](overview.md) — living synthesis across all sources

## Sources
- [제52회 회장기 전국 초등학교 탁구대회 운영 시스템](sources/52nd-president-cup.md) — Python/Flask 기반 4단계 탁구대회 관리 시스템 (예선 리그 → 본선 대진 → 단체전 → 서버 배포)
- [대진표/기록지 출력 표시 개선 작업 (2026-04-10)](sources/20260410-print-display.md) — 인쇄 템플릿 16pt 통일, 개인전 두 줄 표시, 단체전 학교명 축약
- [랭킹 API MySQL 마이그레이션 (2026-04-10)](sources/20260410-ranking-mysql.md) — 랭킹 API SQLite→MySQL 전환, rk_* 테이블 구조, rank_num 예약어 회피
- [2026-04-10 전략 분석 및 다음 단계 계획](sources/20260410-strategy-next-phase.md) — 3일간 현장 교훈 6패턴, FastAPI 전환 결정, 대학연맹 프로젝트 계획
- [20260411 브래킷 버그 수정 & ngtos_tt DB 분석](sources/20260411-bracket-fix-ngtos.md) — print_bracket.html 오른쪽 결과 미표시 버그 수정, Flask 템플릿 이중 경로 혼동, VPS scp 별도 배포 확인, ngtos_tt DB 구조 분석
- [20260411 백업 복원 & 시상 탭 서브탭 정리](sources/20260411-backup-restore-awards-fix.md) — 코드 손상 시 VPS tar.gz 백업 복원, sessionStorage 새로고침 방식 재확인, 시상 탭 남자단체/여자단체 제거
- [20260411 단체전 기록지 교보생명컵 기준 수정 & 16강 game_format 버그](sources/20260411-scoresheet-fix.md) — 기록지 5 sub-row 구조 확정, tbody 분리 렌더링 버그 해결, game_format 기본값 버그 수정
- [20260412 FastAPI 마이그레이션 착수](sources/20260412-fastapi-migration-kickoff.md) — Phase 0~9 계획 수립, Mac mini 테스트 환경 구축, www.sellma.kr 외부 접속 확인, Alembic 초기화
- [20260412 회장기 랭킹 포인트 산출 및 DB 업데이트](sources/20260412-ranking-hoejanggi-update.md) — 4·5·6학년 291명 회장기 포인트 산출, VPS rk_* 테이블 반영, 엑셀 보고서 생성
- [20260413 랭킹 시도 수정 및 버튼 삭제, 최종 백업](sources/20260413-ranking-province-fix-final.md) — province 45건 수정, 랭킹 버튼 삭제, VPS 최종완성 백업
- [20260413 대학연맹 시스템 구축 + DB 분리 + 엑셀 업로드](sources/20260413-university-league-build.md) — DB 연맹별 분리(kettf/kuttf/ktta), 52nd UI 이식, 대학연맹 7탭 구축, 엑셀 파서
- [20260413 대학연맹 완성 — 대진표 + 검사결과 + 일정](sources/20260413-university-complete.md) — 전 종목 대진표(16/32/64강), 검사결과, 일정, OCR, 공방률
- [20260413 대학연맹 시상 + 마무리](sources/20260413-university-awards-final.md) — 시상(상장대장 26-01~28), OCR 최종, 테스트 16항목 통과
- [20260414 랭킹 선수 정보 수정 및 중복 합산](sources/20260414-ranking-player-fixes.md) — 학교명 4건 수정, 이율/황찬율 중복 합산, 380명 확정
- [20260414 대통령기 점수 추가 및 랭킹 UI 업데이트](sources/20260414-ranking-president-cup-add.md) — 대통령기(R2) 추가, 147명 점수, VPS UI 컬럼 추가
- [20260414 단체전 세트 점수 오류 수정 및 검증 로직](sources/20260414-team-score-validation.md) — 세트 스코어 2:0→3:0 수정, 탁구 점수 유효성 검증(듀스/오타) 추가
- [20260414 tt-result 디자인 문서 확정](sources/20260414-tt-result-design.md) — 통합 시스템 Approach C 승인, 대학연맹 4/28~5/2 첫 실전
- [20260416 MindVault 훅 정리 + 단체전 점수 수정](sources/20260416-mindvault-hook-score-fix.md) — Windows 훅 깜빡임 해결, 11:12→11:13 수정, 탁구 규칙 검증 추가
- [20260416 tt-result.sellma.kr 배포 및 시스템 통합](sources/20260416-tt-result-deployment.md) — VPS 배포, DB 이식, 랭킹 6대회, 개인전 모달, 기록지, 관리자 기능, 대학연맹 기권 취소선
- [20260417 대한탁구협회 경기결과 전송 시스템 분석](sources/20260417-ktta-transmission-analysis.md) — KVPN+VPN→대회운영프로그램→result.sports.or.kr 전송 6단계, 자동화 분석
- [20260417 KTTA 경기결과 자동 전송 계획](sources/20260417-ktta-auto-transfer-plan.md) — 로컬DB 삽입 방식 4단계 계획, 매핑 구조, root/password 확인
- [20260418 KTTA 공식 웹사이트 분석](sources/20260418-ktta-web-analysis.md) — ktt.or.kr/koreatabletennis.com PHP 구조, 점수API, 대진표 데이터

## Entities
- [KETTF (한국초등탁구연맹)](entities/KETTF.md) — 회장기 대회 주최 기관, ITTF 규칙 채택
- [제52회 회장기 전국 초등학교 탁구대회](entities/52ndPresidentCup.md) — 2026년 개최, 461명 참가, 10개 부문
- [Sellma (sellma.kr 플랫폼)](entities/Sellma.md) — 배포 도메인 및 m4sellma 동기화 허브 운영
- [UniversityLeague (대학연맹 대회 시스템)](entities/UniversityLeague.md) — 52회 완주 후 FastAPI로 시작할 다음 프로젝트
- [Gnuboard6 (그누보드6)](entities/Gnuboard6.md) — PHP→FastAPI 전환한 한국 최대 BBS, FastAPI 방향 외부 검증

## Concepts
- [RoundRobinLeague (조별 리그전)](concepts/RoundRobinLeague.md) — 예선 방식, 고정 대진 순서, JSON 게임 스코어 저장
- [TieBreakRanking (공방율 동률 처리)](concepts/TieBreakRanking.md) — 승수 → 공방율 → 맞대결 3단계 알고리즘 (ITTF 기반)
- [TournamentBracket (본선 대진표 및 시드 배치)](concepts/TournamentBracket.md) — 16/32강 자동 결정, BYE 처리, 승자 자동 진출
- [TeamMatch (단체전)](concepts/TeamMatch.md) — 학교 대항 7경기 토너먼트, 연맹별 방식 확장 계획
- [ScoreSheet (KTT 표준 기록지)](concepts/ScoreSheet.md) — A4 27열 그리드, 예선 앞장 + 본선 기록지
- [DeploymentArchitecture (배포 아키텍처)](concepts/DeploymentArchitecture.md) — 로컬 SQLite / PythonAnywhere / VPS 3중 환경
- [PlayerVerification (선수 검증 시스템)](concepts/PlayerVerification.md) — 활동제한·이적 선수 자동 검출, 다단 CSV 파싱 주의
- [PrintDisplay (인쇄 표시 개선)](concepts/PrintDisplay.md) — 인쇄 템플릿 is32 패턴, 두 줄 flex 레이아웃, 학교명 축약
- [PlayerRanking (선수 랭킹 시스템)](concepts/PlayerRanking.md) — rk_* MySQL 테이블, rank_num 예약어 회피, 5개 대회 연간 포인트 집계
- [TechStackMigration (기술 스택 전환 계획)](concepts/TechStackMigration.md) — Flask→FastAPI, Vanilla JS→Alpine.js 점진적 전환 결정 및 근거
- [FieldOperationLessons (현장 운영 교훈)](concepts/FieldOperationLessons.md) — 4/8~10 현장 6대 반복 패턴과 해결 방향 + #10 Windows 훅 깜빡임
- [KTTADatabaseSchema (KTTA DB 구조)](concepts/KTTADatabaseSchema.md) — ngtos_tt DB 테이블 분석

## Syntheses
- [탁구경기결과관리시스템 업데이트 계획](syntheses/탁구경기결과관리시스템-업데이트-계획.md) — 52회 현장 사고 분석 기반, Phase 0~3 단계별 계획 및 재발 방지 체크리스트
- [Codex 코드 리뷰 — 호환 라우터](syntheses/20260417-codex-review-compat-router.md) — 인증 누락(P1), 64강 미지원(P1), 사유 미보존(P2)
