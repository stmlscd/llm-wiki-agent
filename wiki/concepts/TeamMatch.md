---
title: "TeamMatch (단체전)"
type: concept
tags: [team-match, tournament, table-tennis, school]
sources: [52nd-president-cup]
last_updated: 2026-04-14
---

# TeamMatch (단체전)

[[52ndPresidentCup]] 3단계에서 추가된 학교 대항 단체전. 성별별 32개 학교가 토너먼트 방식으로 경쟁한다.

## 경기 방식

| 항목 | 내용 |
|------|------|
| 형식 | 7경기: 단식 6경기 + 복식 1경기 |
| 구조 | 32개 학교 토너먼트 (남·여 각각) |
| 진행 | [[TournamentBracket]]과 동일한 브래킷 구조 |

## 단체전 경기 방식 확장 계획

연맹별 상이한 단체전 방식을 사전 정의 선택 구조로 지원할 예정:
- **4단1복**: 단식 4경기 + 복식 1경기 (5경기 총)
- **5단식**: 단식 5경기
- **5단2복**: 단식 5경기 + 복식 2경기 (7경기 총)

## 선수 등록

- 엑셀 파일로 팀 로스터 업로드 (`POST /api/team/upload-players`)
- 성별, 팀명, 선수명, 학년, 선수번호 정보 저장

## game_format 버그 주의 (2026-04-11)

라운드 자동 진출 매치 생성 시 `game_format` 기본값이 `'단식,단식,단식,단식,단식,단식,단식'`(전부 단식)으로 세팅됨. 라운드 생성 후 반드시 명시적 UPDATE 필요:
```sql
UPDATE team_matches SET game_format='단식,단식,복식,복식,단식,단식,단식' WHERE round_name='16강';
```
향후 자동 생성 로직에서 연맹 표준값(단단복복단단단)을 기본값으로 적용해야 함.

## 데이터베이스 스키마

```
team_match_players:
  gender (M/F), team_name, name, grade, player_no
```

## API

```
POST /api/team/upload-players     → 팀 로스터 업로드
GET  /api/team/players/<gender>   → 팀 선수 조회
POST /api/team/draw/<gender>      → 단체전 대진 추첨
GET  /api/team/bracket/<gender>   → 단체전 대진표
POST /api/team/match/<gender>/<round>/<num>  → 단체전 결과 기록
GET  /api/team/match/<gender>/<round>/<num>  → 단체전 결과 조회
```

## 구현 파일

- `dist/routes_team.py` — Flask Blueprint
- `dist/templates/print_team_scoresheet.html` — 단체전 기록지
- `dist/templates/print_team_order.html` — 팀 출전 순서표

## 세트 점수 유효성 검증 (2026-04-14)

단체전 32강 인천창신초 vs 제천홍광초 경기에서 세트 점수 누락 입력으로 3:0이 2:0으로 잘못 기록된 사고를 계기로 이중 검증 도입:

**서버 측**: `set_scores` 배열에서 세트 승수를 직접 재계산. 프론트엔드가 보내는 `player1_score`/`player2_score`를 무시.

**프론트엔드**: `validateSetScore()` 함수로 입력 시점에 차단:
- 11점제 정상 점수: 11:0~11:9
- 듀스 정상: 12:10, 13:11, 14:12... (2점차)
- 차단: 오타(50점 초과), 동점, 11:10(불가능), 듀스 2점차 아님
- 3선승 미달 시 저장 차단

## Connections

- [[TournamentBracket]] — 동일 대진 구조 사용
- [[ScoreSheet]] — 단체전 기록지
- [[52ndPresidentCup]] — 단체전 도입 대회
- [[52nd-president-cup]] — 구현 소스
- [[FieldOperationLessons]] — 데이터 입력 오류 패턴의 구체적 해결
