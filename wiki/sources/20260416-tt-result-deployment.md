---
title: "20260416 tt-result.sellma.kr 배포 및 시스템 통합"
type: source
tags: [deployment, tt-result, fastapi, vps, database, migration, ranking, forfeit, validation]
date: 2026-04-16
source_file: raw/20260416_tt_result_deployment.md
---

## Summary

tt-result.sellma.kr을 VPS에 신규 배포하고, 기존 community.sellma.kr:8000(Flask)의 52회 회장기 초등학교 탁구대회 데이터를 이식. 랭킹 시스템 6개 대회 지원, 개인전 수정 모달, 기록지 버튼, 관리자 기능(순위계산/기권/이동)을 초등연맹·대학연맹 양쪽에 구현. 탁구 11점제 듀스 규칙 검증 로직 VPS 배포.

## Key Claims

- tt-result는 FastAPI+uvicorn 기반, Apache 리버스 프록시(8100 포트), Let's Encrypt SSL
- DB 분리 전략: tt_result(시스템), kettf_db(초등), kuttf_db(대학), ktta_db(협회)
- 8000포트 앱은 kettf_tournament DB, tt-result는 kettf_db 사용 — 혼동 주의
- 초등연맹 기권 = 선수 삭제 + 시드 재정렬, 대학연맹 기권 = is_forfeit 플래그(취소선)
- 랭킹 통합순위는 합계 점수 기준 내림차순 (학년별 분리 아님)
- JS 수정 후 반드시 캐시 버스팅 필요 (?v=timestamp)

## Key Quotes

> "N세트 X:Y — 규칙 위반입니다." — 탁구 점수 검증 통일 메시지

## Connections

- [[DeploymentArchitecture]] — VPS 3중 환경에서 4중 환경(tt-result 추가)으로 확장
- [[FieldOperationLessons]] — #10 Windows 훅 깜빡임, DB 혼동 교훈 추가
- [[TeamMatch]] — 세트 점수 검증 VPS 배포 완료
- [[PlayerRanking]] — 6개 대회 통합, 대통령기 추가, 통합순위 재계산
- [[UniversityLeague]] — 기권 취소선 방식, match-modal 추가
- [[TechStackMigration]] — Flask→FastAPI 실제 이행 시작

## Contradictions

- 없음
