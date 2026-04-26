---
title: "20260426 운영 모델 확정 + 대학연맹전 장소 수정"
type: source
tags: [operating-model, cloud-claude, m4sellma, venue, governance, multi-claude-coordination]
date: 2026-04-26
source_file: raw/20260426_operating_model_and_venue_fix.md
---

## Summary
2026-04-26 D-2 시점에 3개 사건이 동시 발생: (1) 대학연맹전 장소를 인천 계양체육관 → 제천시 어울림체육관으로 수정 (commit ce3b255), (2) m4sellma `~/tt-result/` 가 M1 Max Claude Desktop 지시로 삭제됐다가 Windows PC Claude 가 재생성, (3) 사용자가 의도된 운영 모델을 명문화 — 평시 Windows PC Claude 총괄, 대회 D-0~D+4 MacBook Air 현장, cloud Claude 통합 관제는 API 버그로 보류. `docs/operating-model.md` 신규 작성으로 다중 Claude 세션 간 충돌 방지 기반 마련.

## Key Claims
- claude.ai/code 통합 관제는 **장기 그림이지만 2026-04-26 현재 API 버그로 보류** — fallback 으로 Windows PC Claude 직접 운영
- 다중 Claude 세션이 같은 인프라(m4sellma)에 반대 지시를 내릴 때 **`docs/operating-model.md` 우선 참조** 원칙으로 충돌 방지
- MacBook Air (몽실, M4 Mac) ≠ m4sellma 서버 (Mac mini) — 이름 유사하나 별개. 메모리에 명확화
- 대회 D-0~D+4 는 **Claude 자율 결정 금지**, 사용자 현장 판단 우선
- 대회 장소 변경(인천→제천)은 사실 정정으로 즉시 main 머지 정당. 코드/템플릿/스크립트에 하드코딩된 곳 없음 (PROJECT_BRIEF.md 만 수정)

## Connections
- [[OperatingModel]] — `docs/operating-model.md` 신규 문서 (본 source 의 산출물)
- [[ServerRules]] — §7 운영 워크플로우와 정합 (VPS=정상소스, m4sellma=백업+테스트)
- [[20260425-m1max-cowork-and-m4sellma-sync]] — 어제 git bundle 베이스라인 확립의 후속
- [[AdoptionPolicy]] — §17, cloud Claude 자체도 안정화 후 §17 6항목 분석 적용 대상

## Contradictions
- 외형상 모순: M1 Max Claude (지우기) vs Windows PC Claude (재생성) — 가정(cloud Claude 미래 vs 현재 fallback)이 다른 문제. `docs/operating-model.md` 로 가정 통일 → 모순 해소
