# 2026-04-23 서버 규칙 + 도입 전 분석 정책 제정

## 배경
이번 사이클 동안 다음이 복합적으로 발생:
- BlueKiwi MCP 도입 → WF:4 워크플로우 구축 → Task#1 실행 과정에서 인프라 가정 오류 노출
- m4sellma 구조가 백업이 아닌 "VPS 종속 프록시"였음이 리허설에서 드러남
- 외부 스킬 4건 평가 → 설치 거부 (2026-04-22)
- Autopus-ADK 가 university-league/ 에만 격리된 상태

사용자 결론: **프로젝트 독립성과 안정성을 지키려면 두 가지 규칙이 필요**.

## 규칙 1 — 서버 규칙 (docs/server-rules.md, CLAUDE.md §16)
1. 운영 서버 = **VPS (115.68.177.35)**
2. 백업·테스트 서버 = **m4sellma (192.168.1.202)** — 3역할 겸용 (백업 · 장애 대체 · 테스트)
3. m4sellma 는 **항상 VPS 와 동일 데이터** 유지. 매일 02:00 자동 sync, 대회 중(D-0~D+4)은 수동만.
4. 운영 VPS DB 직접 ALTER/UPDATE 금지 — Alembic 만.
5. m4sellma 앱은 로컬 MySQL 사용 (`APP_SSH_TUNNEL=false`). VPS 터널 구성 복귀 금지.
6. 장애 전환 목표 3분 이내.

## 규칙 2 — 도입 전 분석 정책 (docs/adoption-policy.md, CLAUDE.md §17)
Claude Code Skill · MCP 서버 · BlueKiwi/Autopus 워크플로우 · 외부 에이전트 도구(OpenClaw 등) 도입 전:

**Claude 가 먼저 6항목 분석 → 사용자 허가 → 실행**:
1. 용도 적합성
2. 프로젝트 독립성 영향
3. 안정성 리스크
4. 대체 수단
5. 유지 비용
6. 철회 경로

예외 없음 — 이미 설치된 도구의 활성화 범위 확장도 동일 절차.

## 적용 범위
- 인프라·도구 영역: 적용
- 프로젝트 코드 개발(기능·리팩터): 기존 커밋 규율 따름, 별도 분석 불필요
- 이미 도입된 도구(BlueKiwi WF:4, Graphify, llm-wiki, Autopus): 대회 후 회고(Step 10)에서 가치 재평가

## 등록 위치 3곳
모든 규칙은 아래 3곳에 동기 저장:
1. `docs/*.md` — 공식 문서 (검색 가능)
2. `CLAUDE.md §16, §17` — 매 세션 자동 로드
3. `~/.claude/projects/.../memory/feedback_*.md` — 장기 기억 (다른 세션에서도 적용)

## 의도
- 혼선 방지
- 프로젝트 독립성 유지 (외부 의존 누적 차단)
- 대회 운영 안정성 보호
