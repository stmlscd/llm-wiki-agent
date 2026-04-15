---
title: "20260411 백업 복원 & 시상 탭 서브탭 정리"
type: source
tags: [backup-restore, awards-tab, session-storage, gracket-lite, team-bracket, bug-fix]
date: 2026-04-11
source_file: raw/20260411_backup_restore_awards_fix.md
last_updated: 2026-04-11
---

## Summary

오늘 오전 시상 탭 대진표 결과 반영 시도 중 `main.js` 전체가 손상됨. m4sellma의 `app_20260410_154730.tar.gz` (VPS 스냅샷)을 기준으로 복원. 복원 후 시상 탭에서 남자단체/여자단체 서브탭을 제거하고 검사결과를 기본 탭으로 설정.

## Key Claims

- **백업 tar.gz 위치**: m4sellma `/Volumes/WorkSpace/project_src/claudespace/52nd_backup/app_20260410_154730.tar.gz` — VPS 전체 파일 스냅샷 (DB 포함)
- **sessionStorage 새로고침이 올바른 방식**: 본선리그-개인전 기록 저장 시 sessionStorage에 탭/성별/학년 저장 후 `location.reload()`. DOMContentLoaded에서 복원. SPA 방식(`loadBracketSection` 직접 호출)보다 안정적.
- **단체전 GracketLite 이미 4월 10일 백업에 포함**: `renderTeamBracketTree`가 GracketLite 기반으로 이미 구현됨 — 오늘 새로 작성 불필요했음.
- **시상 서브탭**: 남자단체(team-m)/여자단체(team-f) 제거, 검사결과(verification)를 기본 active 탭으로 변경.
- **loadContentForTab 연동**: awards 탭 진입 시 `loadAwards()` → `loadVerification()` 로 변경하여 검사결과 자동 로드.
- **수정완성본 백업**: `app_20260411_152404_수정완성본.tar.gz` 으로 m4sellma에 저장.

## Key Quotes

> "오늘 백업된것을 이용하면 안돼." — 손상된 오늘 자 백업 대신 4월 10일 백업을 명확히 지정.

> "제대로 되었어. 시상메뉴에서 남자단체/여자단체만 삭제해 주면돼." — 복원 확인 후 추가 정리 요청.

## Connections

- [[DeploymentArchitecture]] — 백업 tar.gz 위치, VPS scp 배포 패턴, 수정완성본 백업 명명 규칙
- [[TeamMatch]] — GracketLite 기반 단체전 대진표가 이미 4/10 백업에 포함됨 확인
- [[PlayerVerification]] — 시상 탭 기본 탭이 검사결과로 변경됨
- [[FieldOperationLessons]] — 코드 손상 시 백업 복원이 디버깅보다 빠름이 재확인

## Contradictions

- 없음. SPA 방식이 sessionStorage 방식보다 깔끔해 보이지만 실제 현장에서는 sessionStorage 방식이 더 안정적임 — [[FieldOperationLessons]]의 "단순함 우선" 원칙과 일치.
