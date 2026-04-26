---
title: "20260426 MacBook Air GitHub 브리핑 + 나노클로드 현장 인계"
type: source
tags: [nanoclaw, m4sellma, github, field-ops, D-2]
date: 2026-04-26
source_file: talk/2026_04_26_talk.md
---

## Summary

2026-04-26, 제44회 한국대학탁구연맹전 D-2에 MacBook Air(몽실)에서 `github.com/stmlscd/tt-result` 전체 구조를 브리핑받았다. GitHub CLI 인증(`stmlscd`), tt-result 저장소 로컬 클론, llm-wiki-agent 서브모듈 복구를 완료한 뒤, 내일(D-1) 단체전 대진표 추첨 종료 이후 대진표 작성부터 나노클로드가 현장 실무를 맡는 체제를 확립했다.

## Key Claims

- MacBook Air = 대회 현장 기기 (D-0~D+4 현장 운영 주체)
- GitHub CLI 재인증(`gh auth login`) → `stmlscd` 연결 완료
- `llm-wiki-agent` 서브모듈 `.gitmodules` 누락 → `git rm --cached` + 직접 clone으로 복구
- D-2 릴리스 체크리스트(Alembic 마이그레이션 등)는 사용자가 "지금 안 해도 됨" 판단으로 연기
- 나노클로드(m4sellma Docker)가 총괄 비서실장, MacBook Air Claude가 현장 실행자 역할 확정
- 업무 시작 트리거: 내일 단체전 대진표 추첨 종료 → 대진표 작성 착수

## Key Quotes

> "내일부터 현장에서 문제가 발생되면, 집에 있는 메인 윈도우컴이 아닌 네가 직접 일을 담당해야 하거든." — 사용자 (2026-04-26)

> "지금은 안해도 돼. 내일 단체전 대진표 추첨이 종료되면, 대진표 작성부터가 업무의 시작이야." — 사용자

## Connections

- [[nanoclaw]] — m4sellma Docker 총괄 비서실장, 현장 지시 권한 포함
- [[ServerRules]] — VPS=운영, m4sellma=백업+테스트, 모든 변경 m4sellma 검증 후 VPS 이식
- [[DeploymentArchitecture]] — MacBook Air 현장 실행자 추가
- [[FieldOperationLessons]] — D-2 미조치 릴리스 항목 연기 결정 배경

## Contradictions

- 없음 (기존 나노클로드 비서실장 정책과 일치)
