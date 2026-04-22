---
title: "AdoptionPolicy"
type: concept
tags: [policy, governance, mcp, workflow, skill]
sources: [20260423-server-rules-and-adoption-policy]
last_updated: 2026-04-23
---

## 정의
Claude Code Skill · MCP 서버 · BlueKiwi·Autopus 워크플로우 · 외부 에이전트 도구 도입 전 분석·결정 절차. 2026-04-23 제정.

## 원칙
1. Claude 가 **먼저** 방향성·적합성·리스크 분석.
2. 결과를 사용자에게 보고, **도입 결정권은 사용자**.
3. 사용자 허가 없이 설치·등록·도입 금지.
4. 이미 설치된 도구의 **활성화 범위 확장** 도 동일 절차.

## 분석 6항목 (의무)
1. 용도 적합성 — 프로젝트가 실제로 필요한가?
2. 프로젝트 독립성 영향 — 외부 의존 증가?
3. 안정성 리스크 — 대회 운영 방해 가능?
4. 대체 수단 — Python 스크립트·기존 규율로 가능?
5. 유지 비용 — 사용자 혼자 유지 가능?
6. 철회 경로 — 제거 단계.

## 적용 범위
**대상**: Claude Code Skills, MCP 서버, BlueKiwi/Autopus 워크플로우, 외부 에이전트(OpenClaw 등), GitHub Actions 변경.

**대상 아님**: 프로젝트 코드 개발(기능·리팩터) — 기존 커밋 원칙 따름.

## 이미 도입된 항목 현황 (2026-04-23)
| 도구 | 상태 | 재검토 |
|------|------|--------|
| BlueKiwi MCP (WF:4) | 활용 중 | 대회 후 Step 10 |
| Graphify | 유지 | 주요 구조 변경 시 |
| llm-wiki-agent | 유지 | 세션마다 |
| Autopus-ADK | university-league/ 격리 | 대회 후 |
| MindVault 훅 | 유지 | - |
| 외부 스킬 4건 | 불필요 판정 (미설치) | color-expert 만 UX 단계 |

## 폐기 기준
- 2개 사이클 연속 사용 0
- 프로젝트 독립성 훼손
- 유지 비용이 가치 초과
- 대회 운영 안정성 위협

## 근거 사례
- **BlueKiwi WF:4**: 도입 후 Step 6 iter 에서 인프라 가정 오류 발견 → 사전 분석 필요성 확인
- **외부 스킬 4건 평가 (2026-04-22)**: 사전 평가로 설치 차단 — 이 정책의 모범
- **Autopus-ADK 격리 배치**: 프로젝트 독립성 유지의 좋은 사례

## Connections
- [[ServerRules]] — 쌍을 이루는 최상위 규칙
- [[BlueKiwi]] — 정책 제정의 주된 근거
- [[TechStackMigration]] — 과거 기술 도입 사례와 비교
