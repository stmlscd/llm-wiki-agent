---
title: "2026-05-03 재연결 세션 — F.zip 압축 해제 + GitHub 동기화"
type: source
tags: [backup, github-sync, deployment, devops, encoding]
date: 2026-05-03
source_file: raw/20260503_reconnect_fzip_github_sync.md
---

## Summary

D+1 세션 이후 컨텍스트 단절로 재연결한 세션. M1 Max에서 백업한 F.zip(15GB)을 m4sellma에 압축 해제하고, GitHub main 브랜치와 26커밋 동기화(Auth 2FA + 대회생성V1 + 데이터격리). llm-wiki-agent 서브모듈 초기화 완료.

## Key Claims

- macOS `unzip`은 Windows CP949 파일명 처리 불가 → Python `zipfile` + `encode('cp437').decode('cp949')` 패턴으로 해결
- 64,556개 파일 추출 성공 (186개 오류는 docs/*.md 한글 파일명 혼용 인코딩)
- GitHub pull 26커밋: `e3e2021` → `042da2f` (2FA 백엔드, 유형 4 통합 화면, tournament_id 데이터 격리)
- llm-wiki-agent 서브모듈 초기화: `git submodule update --init` → `2db4a278`

## Key Quotes

> ZIP 한글 인코딩: macOS `unzip`은 CP949 파일명 처리 불가. Python `zipfile` + `encode('cp437').decode('cp949')` 패턴 필수.

> 메모리 커밋 수 부정확: 세션 메모리에 "20커밋 뒤처짐" 기록됐으나 실제 26개.

## Connections

- [[DeploymentArchitecture]] — F.zip은 M1 Max 개발 허브 전체 백업, 3중 환경의 개발 노드
- [[GitHubSync]] — 26커밋 동기화, 서브모듈 초기화
- [[TwoFactorAuth]] — 이번 pull로 통합된 주요 기능

## Contradictions

없음. 기존 위키의 [[DeploymentArchitecture]] 내용과 일치.
