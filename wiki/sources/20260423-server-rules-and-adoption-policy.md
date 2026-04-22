---
title: "20260423 서버 규칙 + 도입 전 분석 정책 제정"
type: source
tags: [policy, rules, server, adoption, mcp, workflow, governance]
date: 2026-04-23
source_file: raw/20260423_server_rules_and_adoption_policy.md
---

## Summary
두 가지 프로젝트 규칙을 공식 제정: (1) **서버 규칙** — 운영=VPS, 백업/테스트=m4sellma, 상시 동일 데이터. (2) **도입 전 분석 정책** — Skill/MCP/Workflow 도입 전 Claude 가 6항목 분석 → 사용자 허가. 각 규칙은 `docs/*.md` + `CLAUDE.md §16·§17` + memory feedback 3곳에 동기 저장.

## Key Claims
- **프로젝트 독립성**은 도구 도입의 1순위 기준 — 외부 의존 누적은 장기적으로 운영 안정성을 해친다
- 도구 도입 **사후 발견 비용**이 사전 분석 비용보다 훨씬 크다 (예: BlueKiwi 도입 후 Step 6 중 인프라 가정 재조정)
- 규칙은 3곳(공식 문서·CLAUDE 자동 로드·memory)에 동기 저장해야 다른 세션에서도 일관 적용됨
- 이미 도입된 도구(BlueKiwi WF:4, Graphify, llm-wiki, Autopus)는 대회 후 회고(Step 10)에서 가치 재평가
- 프로젝트 코드 개발은 이 정책 대상 아님 (기존 커밋 원칙)

## Connections
- [[ServerRules]] — 규칙 1 (이번에 신설된 개념)
- [[AdoptionPolicy]] — 규칙 2 (이번에 신설된 개념)
- [[BlueKiwi]] — 규칙 제정의 근거 사례
- [[FieldOperationLessons]] — 현장 운영 관점의 정책 필요성

## Contradictions
- 없음 (기존 규칙과 보완 관계)
