---
title: "20260423 HyperFrames tt-result 15초 홍보 영상 제작"
type: source
tags: [hyperframes, marketing, promo-video, gsap, table-tennis, tt-result, visual-identity, wcag, ffmpeg]
date: 2026-04-23
source_file: raw/20260423_hyperframes_promo_video.md
---

## Summary
HyperFrames 스킬로 tt-result 시스템 15초 홍보 영상 컴포지션 제작. 1920×1080, GSAP 타임라인 기반, 단일 `index.html` 구조. 탁구 테이블 위 공 8회 랠리 + 4개 피처 카드 스택 + `TT-Result` 대형 타이틀 리빌 + 페이드. lint/validate 모두 통과(의도적 워터마크 contrast 경고 5건만 잔존). FFmpeg 미설치로 MP4 렌더는 사용자 허가 후 진행. BGM 은 placeholder 주석으로 남겨 두어 파일 드롭 시 자동 연결.

## Key Claims
- Visual Identity Gate 는 DESIGN.md 없이도 프로젝트 컨텍스트(탁구/스포츠테크) + 사용자 지시(공·테이블·TT-Result·은은한 BGM) 만으로 다크 네이비 + 클래식 탁구 블루 + ITTF 오렌지 팔레트 도출 가능
- Scene Transitions 4-rule 은 "단일 scene + 레이어드 요소" 구성으로 자연 회피 가능 — 테이블은 0-15s 유지, 타이틀은 10.3s 에 위로 레이어됨
- `overwrite: "auto"` 가 인접 tween 경계 겹침 경고(`overlapping_gsap_tweens`)의 공식 권장 해결안
- 모든 루프 애니메이션은 `repeat: Math.floor(TOTAL / cycle) - 1` 로 작성해야 15s 컴포지션 overshoot 방지 (Math.ceil 은 린트 경고)
- WCAG AA contrast 경고 중 "의도적 장식"(배경 워터마크)은 허용 범위 — 콘텐츠성 텍스트만 상향 조정 필요
- FFmpeg 은 HyperFrames `render` 의 시스템 의존성. winget/chocolatey 둘 다 PATH 존재하나 미설치 상태. CLAUDE.md §17 도입 정신 따라 사용자 허가 필요

## Composition Structure (15s)
| 구간 | 내용 |
|------|------|
| 0 – 1.5s | 배경 글로우 호흡, 테이블·네트·플로어 라인 등장, HUD + 우상단 버전 뱃지 |
| 1.5 – 2.1s | 공 위에서 테이블 우측으로 낙하 (`power2.in`) + 그림자 등장 |
| 2.1 – 9.3s | 좌↔우 8회 랠리 (0.9s/cycle, 높이 310/270/290/250/280/240/300/190 변주) |
| 3.0 / 4.6 / 6.2 / 7.8s | 좌측 피처 카드 스택 (대회운영/선수등록/경기기록/실시간랭킹) |
| 9.3 – 10.1s | 공 화면 위로 사라짐 (타이틀 브릿지) |
| 10.3 – 12.5s | `TT-Result` 타이틀 리빌 (260px, TT 흰색 + Result 오렌지 + 글로우) |
| 14.3 – 15s | 페이드 투 다크 |

## Technical Decisions
- Track index 분리: 0 audio / 1 HUD / 2 code-tag / 3 stage / 4-7 features / 8 title / 9 fade
- `window.__timelines["main"]` 에 paused 타임라인 등록 (HyperFrames 규약)
- 공·그림자 tween factory `bounce(t, fromX, toX, peakTopY)` 로 8회 반복 로직 압축
- 배경 4종 ambient (bg-glow-1/2, ghost-TT, hud-dot) 은 `gsap.to(...)` 독립 루프 (timeline 밖)
- BGM placeholder: `<audio>` 주석 + AUDIO_PLACEHOLDER 안내 라인. 사용자가 `bgm.mp3` 드롭 후 주석 해제만으로 자동 동기화

## Validation Results
- `npx hyperframes lint`: 0 error / 0 warning (최초 audio src not found 1 error + Math.ceil overshoot + ball/shadow tween 경계 겹침 3 warning 모두 해결)
- `npx hyperframes validate` (WCAG AA): 0 error / 0 warning / 5 contrast warning — 전부 ghost-TT 배경 워터마크 (opacity 0.028, breathing 0.045 peak). 콘텐츠성 텍스트는 `#94a3b8` / `#cbd5e1` 로 상향 조정

## Connections
- [[TTResult]] — 홍보 대상 시스템 본체
- [[Sellma]] — 배포 플랫폼 및 홍보 채널 주체
- [[ClaudeCode]] — HyperFrames 스킬 실행 환경
- [[AdoptionPolicy]] — FFmpeg 설치가 §17 정신 대상으로 사용자 허가 경로 적용

## Open Items
- FFmpeg 설치 승인 대기 (choco / winget / 수동 중 택일)
- BGM 파일 선택 + 저작권 확인 후 `bgm.mp3` 로 저장
- preview → render → 배포(랜딩·SNS·현장 안내) 파이프라인 완성
- 다른 HyperFrames 컴포지션 활용 가능성: 현장 디지털 안내판·선수 소개·결승 하이라이트

## Contradictions
- 없음. Visual Identity Gate 의 "DESIGN.md 필수" 원칙을 인라인 정의로 대체했으나, 작업 후 이를 DESIGN.md 로 문서화해 두면 향후 영상 시리즈 제작 시 일관성 확보 가능 (개선 여지).
