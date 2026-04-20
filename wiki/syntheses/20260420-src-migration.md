---
title: "src/ 구조 마이그레이션"
type: synthesis
tags: [migration, architecture, fastapi]
sources: [52nd-president-cup]
last_updated: 2026-04-20
---

## Summary
university-league/app/ → src/ 구조 전면 이전 완료. CLAUDE.md 목표 구조에 맞춰 44개 Python 파일로 재구성.

## Key Changes
- core/ (config, database, security) — 설정·DB·인증 분리
- models/ (13개) — SQLAlchemy 모델 그대로 이동
- services/ (4개) — bracket_generator, match_orders, match_schedule, tiebreak
- routes/ (15개) — compat_52nd.py(1074행)를 5개로 분리 (league, bracket, team, awards, admin)
- utils/bracket_constants.py — 시드 매핑 상수 공유 모듈
- templates/ + static/ — 복사 (변경 없음)

## compat_52nd.py 분리 매핑
| 원본 함수 | 분리 파일 |
|-----------|----------|
| api_category, api_group, api_save_result, calculate_rankings | routes/league.py |
| api_draw_info, bracket_data, match_save, match_detail, doubles_list, mixed_list | routes/bracket.py |
| team_list ~ team_match_detail (7개) | routes/team.py |
| api_awards, api_univ_awards | routes/awards.py |
| forfeit_player, move_player, singles_forfeit | routes/admin.py |

## Verification
- 35개 모듈 import 전부 성공
- m4sellma에서 uvicorn 기동 후 14개 API 엔드포인트 200 OK
- Python 3.9 호환 수정 (int|None → Optional[int])

## Connections
- [[UniversityLeague]] — Phase 1 배포 완료 후 구조 개선
- [[DeploymentArchitecture]] — VPS는 미적용, m4sellma:8100 폴백

## Decisions
- VPS는 대회(4/28~5/2) 끝날 때까지 기존 university-league/app 코드 유지
- m4sellma:8100이 현장 폴백 서버
- src/ 구조는 대회 후 VPS에 적용 예정
