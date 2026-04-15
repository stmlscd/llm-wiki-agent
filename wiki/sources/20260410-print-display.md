---
title: "대진표/기록지 출력 표시 개선 작업 (2026-04-10)"
type: source
tags: [print, css, bracket, scoresheet, gracket-lite, flask]
date: 2026-04-10
source_file: raw/20260410_print_display.md
---

## Summary

제52회 회장기 대회 운영 시스템에서 인쇄용 출력물의 가독성을 개선한 작업 기록. 모든 인쇄 템플릿의 선수명·학교명 글자 크기를 16pt로 통일하고, 개인전 대진표에서 선수명과 학교명을 두 줄로 분리 표시하도록 변경했다. 단체전 대진표는 학교 이름 축약(초등학교→초)과 긴 이름 자동 줄바꿈을 적용했다.

## Key Claims

- 인쇄용 템플릿(print_scoresheet, print_league, print_bracket, print_team_scoresheet, stage2/scoresheet)의 선수명·학교명 모두 16pt로 통일
- `print_bracket.html` 개인전 대진표: `.box-name`(32인→9pt/16인→13pt) + `.box-team`(32인→7pt/16인→9pt) 두 줄 표시로 변경
- 단체전 gracket-lite: `shortenSchoolName()` 함수로 학교명 축약, `.gk-name-wrap` 컬럼 flex로 줄바꿈 지원
- **CSS 글자 크기 1~2pt 변경은 육안 식별 불가** — 시각적으로 의미 있는 변경이 필요 (15pt→16pt는 식별 불가)
- 브라우저 화면용 gracket-lite.css 글자 크기(12px/10px)는 인쇄용과 별도로 유지
- gunicorn 재로드: `kill -HUP $(pgrep -f 'gunicorn.*wsgi:app' | head -1)`

## Key Quotes

> "답답한 느낌은 없을 것 같아" — 두 줄 표시 적용 후 사용자 확인

## Connections

- [[ScoreSheet]] — 인쇄 글자 크기 16pt 통일 적용 대상
- [[TournamentBracket]] — print_bracket.html 두 줄 표시, 크기 조정
- [[TeamMatch]] — gracket-lite 학교명 축약, 줄바꿈 적용
- [[DeploymentArchitecture]] — VPS 배포 및 gunicorn 재로드 절차
- [[PrintDisplay]] — 인쇄 표시 개선 전반 개념 페이지

## Contradictions

없음.
