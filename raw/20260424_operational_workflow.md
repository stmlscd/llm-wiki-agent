# 2026-04-24 운영 워크플로우 §7 제정

## 배경
사용자 지시:
> "tt-result 시스템의 운영 규칙을 정하자. VPS 서버의 tt-result 는 정상화된 소스로 운영. 저장된 기록들과 소스는 항상 백업 서버로 백업. 운영서버에서 나타난 문제점들은 테스트 서버에서 테스트 후에 안정화가 확인되면 VPS 서버로 이식하는 방법으로 하자. m4sellma는 tt-result 시스템의 백업과 테스트 서버임을 항상 기억하자."

## 반영 위치
- `docs/server-rules.md §7` (§7-1 ~ §7-6)
- `CLAUDE.md §16` (요약, 매 세션 자동 로드)
- `~/.claude/projects/.../memory/feedback_server_rules.md` (장기 기억)

## §7 핵심 4원칙

1. VPS 는 **정상화된 소스로만 운영** — main HEAD + pytest 통과 + m4sellma 검증 완료 코드만.
2. **저장된 기록·소스는 항상 m4sellma 에 백업** — 코드(GitHub 1차, m4sellma 2차)·DB·운영 기록·감사 로그.
3. **운영 문제는 m4sellma 에서 먼저 해결** — VPS 가 관찰 지점, m4sellma 가 실험실.
4. **m4sellma = 백업 + 테스트**. 모든 결정 전 자문: "이 결정이 m4sellma 의 백업·테스트 역할에 부합하는가?"

## 표준 절차 (감지 → 이식)
1. 감지 (VPS `/health`, systemd, 감사 로그, 사용자 제보)
2. m4sellma 재현 (read-only 먼저, 운영 DB write 금지)
3. 수정 (8102 또는 8100 에 코드·설정)
4. 안정화 확인 (pytest·ruff·smoke·규칙 V1.1)
5. 커밋 (GitHub main, submodule 포함)
6. VPS 이식 (deploy_prod.sh 또는 SFTP + systemctl restart, 백업 `*.bak` 생성)
7. VPS 검증 (curl `/health`, 실 증상 해소)
8. 기록 (llm-wiki, memory, 감사 로그)

## 긴급 P0 예외
대회 진행 중 경기 중단 위험 있을 때만 절차 단축. 그 경우에도:
- VPS 백업 파일 생성 후 수정
- m4sellma 폴백 경로 유지 확인
- 사후 반드시 m4sellma 재현 + 정식 PR 환류 (기술 부채 방지)

## 금지 구성
- VPS 에 pytest 미통과 코드 배포
- m4sellma 를 VPS MySQL 참조 구성으로 복귀
- 운영 DB 직접 ALTER/UPDATE
- m4sellma 를 개발 호스트로만 취급 (백업 역할 방치)
- 대회 중 자동 sync

## 점검 체크리스트 (매 사이클 회고)
- VPS 코드 = GitHub main HEAD?
- m4sellma DB row count = VPS 와 일치?
- m4sellma 8100/8101/8102 응답 정상?
- admin_pw_watchdog 24h PASS?
- m4sellma `.env` APP_SSH_TUNNEL=false?
- VPS 장애 모의 시 3분 이내 전환 가능?
