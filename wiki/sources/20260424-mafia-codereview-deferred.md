---
title: "20260424 mafia-codereview-harness plugin 도입 전 분석 → 대회 후 재검토"
type: source
tags: [adoption-policy, plugin, code-review, deferred, mafia-codereview]
date: 2026-04-24
source_file: raw/20260424_mafia_codereview_deferred.md
---

## Summary
사용자가 YouTube 영상 보고 `vibemafiaclub/mafia-codereview-harness` plugin 설치 시도. SSH host key 이슈로 실패 → §17 도입 전 분석 정책 적용 → 6항목 분석 후 **대회 후 재검토로 보류** 결정. §17 정책의 첫 실전 적용 사례. repo 이름 오타(barness → harness) WebFetch 404 로 확인.

## Key Claims
- "AI 자율주행" 비유는 **마케팅 표현**, 실제는 PR 단위 6단계 리뷰 harness
- 기존 도구(BlueKiwi WF:4 · gstack /review · codex · pytest · ruff)로 **80% 커버** — 신규 설치 필요성 낮음
- 2 commit 신생 repo 는 유지보수 연속성 불확실 → 3~6개월 관찰 후 판단이 안전
- §17 정책은 "신규 도구 설치 충동을 걸러내는 관문" 으로 실전에서 작동
- 사용자가 직접 설치 시도 → Claude 가 §17 상기시키고 분석 수행 → 합리적 보류 결정 → **정책의 가치 실증**

## Connections
- [[AdoptionPolicy]] — §17 정책의 첫 실전 적용 사례
- [[BlueKiwi]] — WF:4 Step 4~6 리뷰 구조와 기능 중복

## Contradictions
- 영상의 "자율주행" 표현 vs 실제 기능 (리뷰 자동화) — 마케팅/현실 차이로 해소
