---
title: "20260425 M1 Max Cowork 프레임워크 도입 판단 + m4sellma git baseline 확립"
type: source
tags: [adoption-policy, frameworks, superpowers, gstack, gsd, omx, playwright, hooks, m4sellma, git-bundle, server-rules]
date: 2026-04-25
source_file: raw/20260425_m1max_cowork_and_m4sellma_sync.md
---

## Summary
M1 Max Claude Desktop Cowork 모드에서 작성한 프레임워크 도입 판단 본편(2026-04-24, 189줄) + 영상 분석 부록(2026-04-25, 179줄) 합 368줄을 GitHub push 후 m4sellma 로 동기화. m4sellma 는 GitHub 인증 미설정 + 대회 D-3 → git bundle 우회로 `~/tt-result/` baseline 확립. Superpowers/GSD/GSTACK 모두 보류, Phase A/B/C 시간축, Playwright + Claude Code 훅 Phase C 채택. mafia-codereview 평가는 어제 Windows PC 분석과 완전 일치.

## Key Claims
- §17 도입 전 분석 정책의 4번째 적용 — Pulumi 블로그 + 영상 2건 입력에 대해 모두 보류·반려·이연 결정
- 업로드 code-convention.yaml + adr.yaml 은 TS/Next.js/NestJS 스택 → tt-result Python/FastAPI 와 불일치, 그대로 폐기하고 신규 작성 필요
- Phase A (D-4~D+4) 새 도구 금지 / Phase B (D+1~D+14) 회귀 테스트 박제 / Phase C (D+15~) 체계 정비
- Playwright 실브라우저 QA 는 4형(Output) 버그 — DB 정확/화면 부정확 — 잡는 유일한 방법, Phase C 채택
- Claude Code 훅 (PreToolUse/PostToolUse) 은 이미 있는 메커니즘이라 신규 도구 도입 아님, OMX 개념을 차용해 안전 규율 묶기
- GitHub auth 없는 m4sellma 에 git bundle 로 무인증 동기화 — D-3 인증 셋업 회피
- 본편 §2-3 mafia-codereview 평가 = 어제 Windows PC 실물 CLAUDE.md 재평가와 일치 → §17 정책 일관성

## Connections
- [[AdoptionPolicy]] — §17, 본편이 새 적용 사례
- [[ServerRules]] — §7, m4sellma 가 git baseline 갖춤으로써 §7 충실
- [[20260424-mafia-codereview-reassessment]] — 본편 §2-3 와 동일 결론 (독립 검증)
- [[20260424-mafia-codereview-deferred]] — 최초 §17 분석
- [[Superpowers]] — Phase C 적극 검토 (회귀 테스트 박제 선행)
- [[Playwright]] — Phase C 신규 채택
- [[ClaudeCodeHooks]] — Phase C 신규 채택

## Contradictions
- 없음. 어제 Windows PC 분석과 본편이 정합. 부록은 본편 Phase C 를 2건 확장.

## 핵심 인사이트
**혼란의 원인은 "프레임워크 선택"이 아니라 "적용 타이밍 + 스택 일치 여부"** (본편 §4)
- 외부 프레임워크는 Claude Code 일반 사용자 + JS/TS 스택 + 다인 팀 전제
- tt-result 는 Python/FastAPI 단일 개발자 + 명확한 도메인 + 대회 일정 deadline
- §17 정책이 이 간극을 사전 필터링 → 본 보고서가 정책 가치 입증
