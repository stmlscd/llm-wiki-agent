---
title: "20260424 mafia-codereview-harness 실물 CLAUDE.md 확인 후 §17 재평가"
type: source
tags: [adoption-policy, plugin, code-review, reassessment, mafia-codereview, adr]
date: 2026-04-24
source_file: raw/20260424_mafia_codereview_reassessment.md
---

## Summary
최초 §17 분석 후 사용자가 plugin 실제 CLAUDE.md (`L:\TT-Result_Project\etc\CLAUDE.md`) 확보해서 Claude 에게 재분석 요청. 실물 확인 결과 의외로 가벼운 파이프라인 — 산출물 4개 markdown + YAML 2개 참조, fork/worktree 격리. 결론은 **대회 후 재검토 유지**이나 보류 사유 재정의: "2 commit 신생 repo" 가 아니라 "`code-convention.yaml` + `adr.yaml` 선행 작성이 대회 운영 시간 잠식"

## Key Claims
- plugin 실물은 `.review-artifacts/{branch}/` 에 4 markdown 산출, fork/worktree 로 소스 무수정 보장
- 참조 파일 2개 (`docs/code-convention.yaml`, `docs/adr.yaml`) 작성이 **plugin 가치의 전제조건**
- "근거 없는 취향 리뷰 금지" 는 기존 도구(BlueKiwi/gstack/codex) 에 없는 고유 철학
- tt-result 는 CLAUDE.md 에 산문 규율만 있고 ADR 문화 부재 → YAML 전환이 실질적 선행 작업
- 재평가로 안정성 리스크는 **낮음** 으로 하향, 유지비용은 **중상** 으로 상향

## Connections
- [[20260424-mafia-codereview-deferred]] — 최초 §17 분석 (이 재평가의 대상)
- [[AdoptionPolicy]] — §17 정책, 실물 확인 후 재평가 사이클의 모범 사례
- [[ServerRules]] — §16 과 함께 `adr.yaml` ADR-001 후보
- [[CredentialPolicy]] — §18, `adr.yaml` ADR-003 후보

## Contradictions
- 없음. 같은 결정(대회 후 재검토)을 유지하되 사유만 재정의.

## 재검토 시 선행 작업 순서
1. `docs/code-convention.yaml` ← CLAUDE.md §3~14 구조화
2. `docs/adr.yaml` ← §16·17·18 → ADR-001~003
3. plugin 설치 + 시범 리뷰
4. BlueKiwi WF:4 Step 4~6 대비 고유가치 평가
5. 유지/폐기 최종 결정
