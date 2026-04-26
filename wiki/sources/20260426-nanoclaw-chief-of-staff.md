---
title: "20260426 나노클로드 비서실장 도입 + 운영 위계 재편"
type: source
tags: [nanoclaw, openclaw, operating-model, chief-of-staff, docker, m4sellma, governance]
date: 2026-04-26
source_file: raw/20260426_nanoclaw_chief_of_staff.md
---

## Summary
2026-04-26 오후, 사용자가 m4sellma 에 nanoclaw 를 Docker 로 설치하여 tt-result 시스템의 총괄 비서실장으로 선정. Windows PC Claude(이 세션)는 실행자로 위치 변경. nanoclaw 는 openclaw(Ollama+gemma 테스트용) 의 Claude Code 친화 변형. cloud Claude 통합 관제(API 버그로 보류)의 fallback 으로 자체 비서실장 layer 도입. 인터페이스(지시 전달 방식)는 첫 실 운영 시점에 확정. §17 정식 6항목 분석은 대회 후 환류.

## Key Claims
- nanoclaw = m4sellma Docker 컨테이너 + openclaw 계열 + Claude Code 친화 변형
- 평시·대회·복귀 모든 시점에서 **나노클로드 = 총괄, Windows PC Claude = 실행자** 위계
- 대회 D-0~D+4 동안에도 나노클로드가 현장·긴급 판단 후 업무 지시 발령 가능
- 인터페이스 미확정 — 첫 운영 시점에 GitHub/텔레그램/SSH/webhook 중 결정
- §17 정식 분석은 대회 후로 이연 (D-2 시간 부족 + 운영 안정화 후 평가가 정확)
- Windows PC Claude 의 자기 인식 변경: "총괄" → "실행자"

## Connections
- [[OperatingModel]] — `docs/operating-model.md` 위계 구조 §1 신설 (본 source 의 후속 갱신)
- [[20260426-operating-model-and-venue-fix]] — 같은 날 오전 정의된 운영 모델의 위계 보강
- [[Openclaw]] — m4sellma `~/.openclaw/` Ollama+gemma 테스트, 본 nanoclaw 의 모태
- [[AdoptionPolicy]] — §17 6항목 분석은 대회 후 환류 (도입 사실만 선기록)
- [[M4sellma]] — Mac mini, nanoclaw Docker 호스트로 추가 역할

## Contradictions
- 오전 운영 모델 ("Windows PC Claude 총괄") vs 오후 갱신 ("나노클로드 총괄, Windows PC Claude 실행") — 같은 날 정책 진화로 해소. 오후 버전이 최신 권위
