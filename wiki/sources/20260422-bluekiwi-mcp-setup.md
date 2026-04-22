---
title: "20260422 BlueKiwi MCP 서버 설치 및 연결"
type: source
tags: [bluekiwi, mcp, workflow, agent-runtime]
date: 2026-04-22
source_file: raw/20260422_bluekiwi_mcp_setup.md
---

## Summary
BlueKiwi 오픈소스 워크플로우 런타임을 설치하고 Claude Code/Desktop에 MCP 서버로 연결 완료. 프로필 tt-result, 8개 런타임 연동.

## Key Claims
- BlueKiwi는 self-hosted 워크플로우 에이전트 런타임
- MCP(Model Context Protocol)로 Claude Code와 연결
- 로컬 SQLite 기반으로 데이터 저장
- Claude Code는 세션 재시작 필요 (실행 중 MCP 변경 미반영)

## Connections
- [[DeploymentArchitecture]] — 개발 도구 인프라 확장
- [[TechStackMigration]] — 에이전트 런타임 도입

## Contradictions
- 없음
