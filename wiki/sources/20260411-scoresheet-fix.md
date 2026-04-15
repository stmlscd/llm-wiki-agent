---
title: "단체전 기록지 교보생명컵 기준 수정 & 16강 game_format 버그 수정"
type: source
tags: [team-match, score-sheet, bug-fix, html-template]
date: 2026-04-11
source_file: raw/20260411_scoresheet_fix.md
---

## Summary
16강 team_matches의 game_format이 전부 단식으로 잘못 세팅된 버그를 수정하고, 단체전 기록지(print_team_scoresheet.html)를 교보생명컵 기준으로 전면 재작성했다. 핵심은 경기당 5개 sub-row 구조 복원, Adviser/Winner/Signature를 단일 tbody로 통합(분리 렌더링 버그 해결), 선수명+승수박스 rowspan=5 통합이다.

## Key Claims
- 16강 자동 생성 매치의 `game_format` 기본값은 전부 단식(`단식×7`)이므로 라운드 진출 시 명시적 UPDATE 필요
- 대진표 오전/오후 차이는 시드 데이터 변경이 아닌 `print_bracket.html` 인덱스 공식 수정(4/11 오전)이 원인
- 복수 `<tbody>` 사이에 `bl-body-td` 구분 tbody를 두면 브라우저가 테이블을 분리 렌더링해 레이아웃이 깨짐
- 단체전 기록지는 경기당 5개 sub-row(5.5mm/행) 구조여야 자연스러운 가로 구분선이 생김

## Key Quotes
> "bl-body-td 별도 tbody 구조가 Winner/Signature를 오른쪽으로 분리 렌더링시키는 근본 원인이었다"

## Connections
- [[TeamMatch]] — game_format 버그 및 기록지 레이아웃 수정 대상
- [[ScoreSheet]] — print_team_scoresheet.html 교보생명컵 기준 전면 개정
- [[FieldOperationLessons]] — tbody 분리 렌더링 버그: 새로운 현장 교훈 추가
- [[TournamentBracket]] — 대진표 오전/오후 차이 원인 조사 결과 (데이터 동일 확인)

## Contradictions
- 없음
