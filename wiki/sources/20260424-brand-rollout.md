---
title: "20260424 TT-Result 브랜드 실배포 (VPS + m4sellma)"
type: source
tags: [branding, deployment, vps, m4sellma, css-variables, hyperframes]
date: 2026-04-24
source_file: raw/20260424_brand_rollout.md
---

## Summary
어제 문서화했던 TT-Result 브랜드 스펙(히어로 프레임 기반)을 사용자 요청으로 대회 D-4 임에도 즉시 양쪽 서버(VPS `tt-result.sellma.kr` · m4sellma 8100/8101/8102)에 배포. 기존 기능(alpine.js mainApp, 메뉴 4개 클릭 로직) 보존하고 헤더 + CSS 변수만 추가. HTTP 200·body 에 `brand-logo`/`class="tt"` 검증 완료.

## Key Claims
- 브랜드 스킨 덧입히기 전략(기존 스타일 위 CSS 변수 + 선택적 override)은 **대회 운영 중에도 비교적 안전한 UI 변경 방법**
- 헤더만 브랜드화하고 **메뉴 이름은 한글 유지** → 운영자 혼란 없이 시각적 완성도만 올림
- `*.bak-YYYYMMDD_HHMMSS` 백업 후 배포 → 이슈 시 단일 `cp` 복원
- HyperFrames 영상 자산을 실서비스 디자인으로 재활용 = 브랜드 통일성 + 제작 비용 절감

## Connections
- [[Sellma]] — sellma.kr 도메인 운영 주체
- [[DeploymentArchitecture]] — VPS + m4sellma 핫 스탠바이 구성
- [[ServerRules]] — m4sellma 검증 → VPS 배포 순서 준수

## Contradictions
- 없음
