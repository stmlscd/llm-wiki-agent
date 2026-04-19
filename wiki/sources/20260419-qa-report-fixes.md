---
title: "QA 검증 및 보안/인증 수정 (2026-04-19)"
type: source
tags: [qa, security, auth, tt-result, fix]
date: 2026-04-19
source_file: QA 실행 결과
---

## Summary

tt-result.sellma.kr에 대해 QA를 실시하여 7개 이슈를 발견하고, CRITICAL 1건 + HIGH 1건을 즉시 수정했다. 2건은 오탐(세션/URL 문제)으로 확인, 3건은 다음 검증으로 이관.

## QA 결과 (Health Score: 52 → 72)

| # | 심각도 | 문제 | 최종 상태 |
|---|--------|------|----------|
| 001 | CRITICAL | 쓰기 API 인증 없음 | **FIXED** — 8개 POST에 `require_login` 추가 |
| 002 | HIGH | 로그인 비밀번호 불일치 | **FIXED** — werkzeug 해시 재생성 |
| 003 | HIGH | 대학연맹 대진표 0건 | **NOT A BUG** — 세션 필요 (정상 동작) |
| 004 | HIGH | 랭킹 API 404 | deferred — 라우트 미구현 |
| 005 | MEDIUM | /records/ 경로 404 | **NOT A BUG** — `/records/elementary/1` 정상 |
| 006 | MEDIUM | 개인전 인쇄 404 | deferred — 팀 기록지는 정상, 개인전 일부 미구현 |
| 007 | LOW | 메인 UX | deferred — 사용자 편의성 디자인 변경 계획 |

## 수정 상세

### ISSUE-001: POST API 인증 추가 (compat_52nd.py)
```python
# 추가된 import
from ..auth.dependencies import get_current_user, require_login

# 8개 POST 엔드포인트에 추가
async def match_save(..., user=Depends(require_login)):
async def team_match_order(..., user=Depends(require_login)):
async def team_match_game_result(..., user=Depends(require_login)):
async def calculate_rankings(..., user=Depends(require_login)):
async def api_save_result(..., user=Depends(require_login)):
# + api_forfeit, api_move_player, api_singles_forfeit
```

검증: 비인증 POST → 401 "로그인이 필요합니다", 인증 POST → 200

### ISSUE-002: 비밀번호 재설정
- tt_result.users + kuttf_db.users의 password_hash를 `werkzeug.generate_password_hash('vm0701')` 로 재생성
- VPS에서 Python 스크립트로 pymysql 직접 업데이트 (MySQL CLI $ 이스케이프 문제 회피)

## Connections

- [[TTResultRules]] — 규칙 준수 검증
- [[DeploymentArchitecture]] — VPS 보안 강화
- [[FieldOperationLessons]] — 인증 누락은 현장 사고 위험

## Contradictions

- 없음
