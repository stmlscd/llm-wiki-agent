---
title: "대학연맹 시스템 완성 — 전 종목 대진표 + 검사결과 + 일정 (2026-04-13)"
type: source
tags: [university-league, bracket, verification, schedule, ocr, tiebreak]
date: 2026-04-13
source_file: raw/20260413_university_complete.md
---

## Summary

대학연맹 시스템의 전 종목(단체전/개인전/복식/혼합) 대진표 자동 생성, 경기결과 입력, 기록지 인쇄, 선수 검증, 대회 일정 표시를 완성했다. OCR은 손글씨 한글 한계로 보조 기능으로 유지하고, 팀목록에서 대진번호 배정하는 방식을 메인으로 확정.

## Key Claims

- bracket_generator.py: 16/32/64강 대진표 자동 생성 + BYE 자동 진출 + 빈 라운드 미리 생성
- 대진번호 배정 UI: 등록된 팀/진출자 목록에서 번호 입력 → 빈 번호 자동 BYE
- 기록지: SINGLES/DOUBLES/MIXED DOUBLES/TEAM SCORE SHEET 자동 표기, BYE 제외 출력
- 검사결과: 개인전+단체전 선수만 크로스체크, 복식 묶음 제외, 오타 발견 실효성 입증
- OCR: Claude Vision/PaddleOCR/Tesseract 모두 손글씨 한글 부정확 → 보조 기능
- 공방률: 52nd tiebreak.py 직접 이식, 2인 승자승 / 3인+ 게임·점수 득실률

## Connections

- [[UniversityLeague]] — 전 종목 시스템 완성
- [[TieBreakRanking]] — 공방률 알고리즘 대학연맹에 이식
- [[FieldOperationLessons]] — OCR 한계, 대진번호 배정 UI가 현장에서 더 효율적
- [[ScoreSheet]] — DOUBLES/MIXED DOUBLES SCORE SHEET 타이틀 자동 표기

## Contradictions

- 없음
