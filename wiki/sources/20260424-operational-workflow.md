---
title: "20260424 운영 워크플로우 §7 제정"
type: source
tags: [rules, governance, operational-workflow, server-rules, backup, testing]
date: 2026-04-24
source_file: raw/20260424_operational_workflow.md
---

## Summary
사용자 지시로 `docs/server-rules.md §7` 에 운영 워크플로우 공식화. 핵심: VPS 는 정상화된 소스만 운영, m4sellma 에 항상 백업, 문제 발생 시 m4sellma 에서 먼저 해결 후 VPS 이식. 8단계 표준 절차 + 긴급 P0 예외 + 금지 구성 + 점검 체크리스트 포함. CLAUDE.md §16 과 memory feedback 에도 동기 반영.

## Key Claims
- VPS 에 **검증 안 된 hot-fix 직접 배포 금지** — 운영 트래픽과 테스트 코드 분리가 안정성의 핵심
- "m4sellma 재현 → 수정 → 안정화 확인" 단계가 **모든 변경의 필수 경로**, 긴급 P0 도 사후 환류 필요
- **백업은 여러 레이어**: GitHub main(1차) + m4sellma rsync(2차) + DB dump + 감사 로그 off-server 복제
- m4sellma 를 개발 호스트로만 취급하는 구성은 **역할 위반** — 백업·테스트가 본연의 용도
- 체크리스트(§7-5) 는 매 대회 사이클 회고 시 실행 → 규율 드리프트 방지

## Connections
- [[ServerRules]] — 이 워크플로우의 상위 문서 (§7 신규)
- [[AdoptionPolicy]] — 새 도구 도입 시 §17 분석과 본 §7 절차가 순서대로 적용
- [[FieldOperationLessons]] — 대회 현장 긴급 수정 패턴(feedback_emergency_server) 과 연계
- [[DeploymentArchitecture]] — 배포 경로 구체화

## Contradictions
- 없음. 기존 서버 규칙을 구체 절차로 확장한 것.
