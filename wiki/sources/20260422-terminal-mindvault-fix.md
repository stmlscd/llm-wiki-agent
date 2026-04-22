---
title: "20260422 MindVault 훅 수정 + Windows 터미널 선택"
type: source
tags: [mindvault, hook, terminal, alacritty, windows]
date: 2026-04-22
source_file: raw/20260422_terminal_mindvault_fix.md
---

## Summary
MindVault 훅의 Windows 터미널 깜빡임 문제를 CREATE_NO_WINDOW 플래그로 해결. Windows용 터미널 에뮬레이터 6개 비교 후 Alacritty 포터블 선택.

## Key Claims
- CREATE_NO_WINDOW (0x08000000)로 콘솔 창 생성 없이 프로세스 실행 가능
- Alacritty는 Rust 기반 GPU 가속으로 최고 속도
- tmux 자체 분할이 없어 tmux에 완전 위임하는 구조가 강점
- Warp는 2026-04 기준 Windows 미지원

## Connections
- [[FieldOperationLessons]] — #10 Windows 훅 깜빡임 문제의 최종 해결
- [[DeploymentArchitecture]] — 개발 환경 터미널 도구 선택

## Contradictions
- 없음
