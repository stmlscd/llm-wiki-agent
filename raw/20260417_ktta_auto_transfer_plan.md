# KTTA 경기결과 자동 전송 계획 — 로컬 DB 삽입 방식

## 목표
tt-result 시스템의 경기 결과를 KTTA 대회운영 프로그램의 로컬 DB(ngtos_tt)에 자동 삽입하여,
프로그램의 "대회정보 전송" 기능만으로 result.sports.or.kr에 전송.

## 접속 정보

| 대상 | 접속 | 계정 |
|------|------|------|
| 로컬 MariaDB | 127.0.0.1:3307 | root / password |
| 원격 DB | 182.162.101.245:13834 | 포트 OPEN, 계정 미확인 |
| result.sports.or.kr | HTTPS 접근 가능 | 웹 API (인증 필요) |
| KVPN | 220.117.160.242:1723 | koreatt2 계정 |

## 데이터 매핑 (tt-result → ngtos_tt)

### 기본 키 구조
- CLASS_CD = '09' (탁구 고정)
- TO_CD = 대회코드 (예: '202614448')
- KIND_CD = 종별코드 (A=일반, 등)
- DETAIL_CLASS_CD = 세부종목코드 (예: '09A11')

### 테이블 매핑

#### 1. 대회 정보
| ngtos_tt | tt-result | 설명 |
|----------|-----------|------|
| tour | tournament_info | 대회 마스터 (TO_CD, TO_NM, TO_YYYY, TO_PERIOD) |
| detail_class_cd | categories (생성 필요) | 세부종목 (남자4학년=09M04 등) |

#### 2. 선수/팀 등록
| ngtos_tt | tt-result | 설명 |
|----------|-----------|------|
| tourplayer | kettf_db.players | 참가선수 (ID_NO, NAME, TEAM) |
| tourplayerdetail | kettf_db.players | 선수별 세부종목 배정 |
| tourteam | kettf_db.team_players | 참가팀 |

#### 3. 예선리그 (nsl*)
| ngtos_tt | tt-result | 설명 |
|----------|-----------|------|
| nslgroup | kettf_db.players GROUP BY group_name | 예선 조 구성 |
| nsldaejin | 경기 순서 매핑 | 예선 대진 배정 |
| nslresult | kettf_db.results | 예선 경기결과 (WINNER_GB, SCORE_L/R) |
| nslresultdetail | results.game_scores JSON → 세트별 행 | 세트별 스코어 |
| nslresult_sum | kettf_db.tiebreak_results | 예선 순위 합산 |
| nslschedule | 생성 필요 | 경기 일정 |

#### 4. 본선 토너먼트 (nst*, nsd*)
| ngtos_tt | tt-result | 설명 |
|----------|-----------|------|
| nsddaejin | kettf_db.draw_results + tournament_matches | 토너먼트 대진 |
| nstresult | kettf_db.tournament_matches | 토너먼트 경기결과 |
| nstresultdetail | tournament_matches.game_scores → 행 변환 | 세트별 스코어 |
| matchschedule | 생성 필요 | 경기 일정 |

### 핵심 변환 로직

#### game_scores JSON → nslresultdetail/nstresultdetail 행
```
tt-result: [{"s1": 11, "s2": 8}, {"s1": 9, "s2": 11}, {"s1": 11, "s2": 5}]

→ nslresultdetail:
  SEQ=1, DETAIL_SCORE_L=11, DETAIL_SCORE_R=8, DETAIL_ROUND_NM='1세트'
  SEQ=2, DETAIL_SCORE_L=9,  DETAIL_SCORE_R=11, DETAIL_ROUND_NM='2세트'
  SEQ=3, DETAIL_SCORE_L=11, DETAIL_SCORE_R=5,  DETAIL_ROUND_NM='3세트'
```

#### 카테고리 → DETAIL_CLASS_CD 매핑
| tt-result category | DETAIL_CLASS_CD | DETAIL_CLASS_NM | SEX_CD |
|-------------------|-----------------|-----------------|--------|
| 남자4 | (확인 필요) | 남자초등4학년 | M |
| 남자5 | (확인 필요) | 남자초등5학년 | M |
| 남자6 | (확인 필요) | 남자초등6학년 | M |
| 여자4 | (확인 필요) | 여자초등4학년 | W |
| 여자5 | (확인 필요) | 여자초등5학년 | W |
| 여자6 | (확인 필요) | 여자초등6학년 | W |
| 남자1-2 | (확인 필요) | 남자초등1-2학년 | M |
| 남자3 | (확인 필요) | 남자초등3학년 | M |
| 여자1-3 | (확인 필요) | 여자초등1-3학년 | W |

## 개발 단계

### Phase 1: 매핑 스크립트 개발
1. tt-result DB → ngtos_tt 형식 변환 스크립트 작성
2. 52회 회장기 데이터로 테스트 (원격 DB 데이터와 비교 검증)
3. DETAIL_CLASS_CD 매핑 테이블 확정 (원격 DB에서 확인 또는 프로그램에서 확인)

### Phase 2: 로컬 DB 삽입 테스트
1. 로컬 DB에 변환된 데이터 삽입
2. 대회운영 프로그램에서 데이터가 정상 표시되는지 확인
3. 문제점 수정 및 재삽입

### Phase 3: 전송 테스트
1. VPN 연결 후 대회운영 프로그램에서 "대회정보 전송" 실행
2. result.sports.or.kr에 정상 전송되는지 확인
3. 전송 로그 검증

### Phase 4: 대학연맹 적용 (4/28 대회)
1. 대학연맹 대회 결과를 동일 방식으로 변환
2. 현장에서 전송 테스트

## 필요한 확인 사항
1. DETAIL_CLASS_CD 코드 — 원격 DB 접속 또는 프로그램에서 확인
2. 선수 ID_NO — KTTA 선수번호 매핑 (tourplayer 필수 필드)
3. KIND_CD — 종별 코드 매핑
4. entrant_team_cd — 팀 코드 매핑

## 이전 실패 원인 분석 및 회피
- UI 자동화 실패 → 회피: DB 직접 삽입으로 UI 우회
- DB 직접 전송 실패 → 회피: 전송은 프로그램 내장 기능 사용
- 프로그램 매칭 실패 → 회피: 데이터만 삽입, 나머지는 프로그램에 위임
