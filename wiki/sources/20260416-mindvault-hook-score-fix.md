---
title: "20260416 MindVault 훅 정리 + 단체전 점수 수정 + 서버 검증"
type: source
tags: [mindvault, hooks, windows, score-validation, database, kettf-tournament]
date: 2026-04-16
source_file: raw/20260416_mindvault_hook_fix_score.md
---

## Summary

Windows에서 Claude Code 사용 시 MindVault PostToolUse 훅이 커맨드 창 깜빡임을 유발하여 2개 훅을 제거하고 UserPromptSubmit 훅만 유지. 단체전 여자 32강 점수 11:12→11:13 수정 시 잘못된 DB(kettf_db)에서 수정하여 반영 안 되는 문제 발생, 올바른 DB(kettf_tournament)에서 재수정 완료. 탁구 11점제 듀스 규칙 검증 함수를 VPS routes_team.py에 추가.

## Key Claims

- Windows에서 PostToolUse 훅은 콘솔 프로세스 호출마다 cmd 창이 순간 생성/소멸하여 깜빡임 유발
- VPS Flask 앱은 `kettf_tournament` DB를 사용 (db.py 확인 필수), `kettf_db`와는 별개
- `_validate_set_score()` 함수로 탁구 규칙(11점제 + 듀스 2점차) 서버 검증 추가
- llm-wiki-agent 서브모듈을 stmlscd/llm-wiki-agent로 fork하여 push 권한 확보

## Key Quotes

> "N세트 X:Y — 규칙 위반입니다." — 검증 실패 시 통일된 안내 메시지

## Connections

- [[FieldOperationLessons]] — 교훈 #10 추가 (Windows 훅 깜빡임)
- [[TeamMatch]] — 단체전 점수 수정 및 검증 로직
- [[DeploymentArchitecture]] — kettf_tournament vs kettf_db DB 구분

## Contradictions

- 없음
