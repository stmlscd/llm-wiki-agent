---
title: "대학연맹 시스템 구축 + DB 분리 + 엑셀 업로드 (2026-04-13)"
type: source
tags: [university-league, mysql, db-separation, excel-upload, fastapi]
date: 2026-04-13
source_file: raw/20260413_university_league_build.md
---

## Summary

52nd UI를 새 시스템에 이식하고, DB를 연맹별(kettf_db/kuttf_db/ktta_db)로 분리했다. 대학연맹 전용 대회관리 시스템(7개 탭)을 구축하고, 참가신청명단 엑셀 자동 파서를 구현하여 제44회 연맹전 데이터(118명 단체, 124명 개인, 107조 복식)를 kuttf_db에 등록했다.

## Key Claims

- DB 연맹별 분리: kettf_db(초등), kuttf_db(대학), ktta_db(협회), tt_result(시스템)
- 52nd UI를 읽기 전용으로 이식, API 응답 형식을 52nd Flask와 완전 동일하게 맞춤
- 대학연맹 탭 구조: 단체전/개인전/개인복식/혼합복식/선발전/일정/검사결과
- 단체전 경기방식: 4단1복/5단식 선택 가능
- 모든 토너먼트 추첨: 수동입력 + OCR 판독 방식
- 메인 메뉴 4개: 대회등록/대회관리/대회기록확인/관리자등록
- 종료 대회는 52nd UI 읽기 전용, 관리 대회는 IS_ADMIN=true

## Connections

- [[UniversityLeague]] — 대학연맹 시스템 실제 구현
- [[DeploymentArchitecture]] — DB 분리, kettf_db/kuttf_db/ktta_db 구조
- [[TechStackMigration]] — FastAPI + SQLAlchemy 멀티 DB 구현
- [[FieldOperationLessons]] — API 응답 형식 호환 교훈 (직접 배열 반환 필수)

## Contradictions

- 없음. 기존 계획의 구체적 실행.
