# 2026-04-17 대한탁구협회 경기결과 전송 시스템 영상 분석

## 분석 대상
- video/2026-04-17 19-40-24.mp4 (1.5분) — KVPN 로그인
- video/2026-04-17 19-54-09.mp4 (3.3분) — 프로그램 로그인 + 전송 + 실적관리
- video/2026-04-17 18-17-26.mp4 (18.7분) — 경기결과 입력 과정

## 전송 절차 (6단계)

### 1단계: KVPN 인증센터 로그인
- 프로그램: KVPN[Cert] 인증센터 (대한체육회)
- 계정: 체육정보시스템 ID/PW (jsong13)
- 목적: VPN 연결을 위한 사전 인증

### 2단계: VPN 연결
- KVPN 로그인 후 VPN 버튼 클릭
- PPTP 프로토콜로 대한탁구협회 내부 네트워크 연결
- 2026년 3월부터 변경된 방식 (이전에는 VPN 불필요했을 수 있음)

### 3단계: 탁구 대회운영 프로그램 실행
- 프로그램명: 탁구 대회운영 프로그램 (Table Tennis competition management program)
- 운영: 국민체육진흥공단(KSPO) / 국민체육진흥기금
- LOGIN 설정:
  - 종목: (09) 탁구
  - 감기방식: 일반
  - 운영형태: VPN+제휴
  - 대회운영서버: https://result.sports.or.kr
  - 로컬 DB 서버: 182.162.101.245:13834
  - 사용자 ID: tos09
  - 버전: Ver. 2025.10.18 14:00

### 4단계: 대회 선택 및 경기 결과 입력
- 대회코드: 202614448
- 대회명: 제52회 회장기 전국초등학교 탁구대회
- 시작일자: 20260408, 종료일자: 20260412
- 좌측 메뉴: 경기진행 > 토너먼트 > 토너먼트 결과입력
- 세부종목별(남자초등4학년, 5학년, 6학년 등) 라운드별 결과 입력
- 각 경기: 선수A/B, 시도, 팀, 승패, 세트 스코어(1~7세트), 양자표 체크

### 5단계: 대회정보 전송
- 좌측 메뉴: 대회운영연계 > 대회정보 전송
- 연도 2026 조회 → 대회 선택 → 전송 버튼
- 전송 테이블 순서 (로그에서 확인):
  1. tour (대회 기본정보)
  2. kind_cd (종목 코드)
  3. reason_cd (사유 코드)
  4. rh_cd (RH 코드)
  5. detail_class_cd (세부종목 코드)
  6. tourplayer (대회 참가선수)
  7. tourplayerdetail (참가선수 상세)
  8. tourteam (대회 참가팀)
  9. matchschedule (경기 일정)
  10. nslresultdetail (예선 결과 상세)
  11. nslresult (예선 결과)
  12. nslschedule (예선 일정)
  13. nsldaejin (예선 대진)
  14. nslgroup (예선 그룹)
  15. nstresult_team (본선 팀 결과)
  16. nstresultrank (본선 순위 결과)
  17. nslresult_sum (예선 결과 합계)
  18. nsddaejin (본선 대진)
  19. nstresult (본선 결과)
  20. nstresultdetail (본선 결과 상세)
- 완료 시 "작업을 완료하였습니다" 알림

### 6단계: 실적관리
- 좌측 메뉴: 실적관리 > 개인실적 / 팀실적
- 생성(개인실적) 버튼으로 실적 데이터 생성
- 전송(개인실적) 버튼으로 서버 전송
- 필드: 선수번호, 세부종목, 순위, 순번, 이름, 소속팀, 참가인원, 임시순위, 자동생성, 전송여부, 삭제여부, 결과, 단체전여부, 선수번호, 전문/생활

## 시스템 구조

### 네트워크
- 인터넷 → KVPN 인증 → VPN(PPTP) → 내부 네트워크
- 내부 서버: result.sports.or.kr (대회운영서버)
- 로컬 DB: 182.162.101.245:13834 (VPN 내부에서만 접근)

### 대회운영 프로그램 메뉴 구조
- 홈
- 시스템
- 등록관리
- 대회관리
  - 대회목록, 리그전 그룹(조), 리그전 대진추첨, 리그전 일정
  - 리그전 결과입력, 리그전 결과처리
  - 토너먼트, 토너먼트 대진추첨, 토너먼트 일정, 토너먼트 결과입력
- 경기진행
- 보고서
- 실적관리: 개인실적, 팀실적
- 대회운영연계: 대회정보 전송

### DB 테이블 구조 (전송 로그에서 추정)
- nsl* 테이블: 예선(리그전) 관련 (National Sports League)
  - nslgroup: 예선 조 정보
  - nsldaejin: 예선 대진
  - nslschedule: 예선 일정
  - nslresult: 예선 결과
  - nslresultdetail: 예선 결과 상세 (세트 스코어)
  - nslresult_sum: 예선 결과 합계
- nst* 테이블: 본선(토너먼트) 관련 (National Sports Tournament)
  - nstresult: 본선 결과
  - nstresultdetail: 본선 결과 상세
  - nstresultrank: 본선 순위
  - nstresult_team: 본선 팀 결과
- nsd* 테이블: 본선 대진 (National Sports Draw)
  - nsddaejin: 본선 대진표

## 자동화 가능성 분석

### 현재 제약 사항
1. KVPN 인증 + VPN 연결이 수동 선행 조건
2. 대회운영 프로그램이 Windows 데스크톱 앱 (웹 API 아님)
3. result.sports.or.kr은 VPN 내부에서만 접근 가능
4. 로컬 DB(182.162.101.245:13834)도 VPN 연결 시에만 접근

### 가능한 접근법
1. **로컬 DB 직접 삽입**: VPN 연결 후 로컬 DB 구조를 분석하여 tt-result 데이터를 변환 삽입, 전송만 프로그램으로 수행
2. **result.sports.or.kr API 역공학**: 전송 로그의 테이블명으로 API 엔드포인트 추측, VPN 내에서 직접 HTTP 전송
3. **UI 자동화**: pyautogui 등으로 대회운영 프로그램 UI를 자동 조작 (가장 비효율적)

### 추천 전략
로컬 DB 직접 삽입이 가장 현실적. tt-result의 데이터(players, results, tournament_matches, team_matches)를 KTTA DB 형식(nsl*, nst*, nsd* 테이블)으로 변환하는 매퍼를 개발하면, 경기 결과 입력 단계(4단계)를 완전히 생략할 수 있음. VPN 연결(1~2단계)과 전송(5단계)만 수동 수행.
