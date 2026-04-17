# 2026-04-17 KTTA 원격 DB 접속 성공 및 데이터 구조 검증

## 핵심 성과

### 원격 DB 인증정보 확보
- **방법**: ngtos_tt.exe (PID 14088) 프로세스 메모리 스캔으로 인증정보 추출
- **서버**: 182.162.101.245:13834 (MariaDB 11.4.2)
- **계정**: jinjin / jinjin2024
- **DB**: ngtos_tt
- **VPN**: KVPN (220.117.160.242:1723, koreatt2 계정) 연결 필수

### 로컬 DB 인증정보
- **서버**: 127.0.0.1:3307 (MariaDB 10.8.3)
- **계정**: root / password
- **로컬 DB는 캐시용** — 프로그램은 원격 DB에 직접 연결하여 운영

## 실제 데이터 구조 (52회 회장기 기준)

### detail_class_cd — 세부종목
- 4~6학년 개인전만 6개 (09F11~09K11): DETAIL_CLASS_NM = "개인전"
- 단체전 2개 (09U01=남자, 09W01=여자): DETAIL_CLASS_NM = "단체전"
- 1-2학년, 3학년은 detail_class_cd에 없음 (별도 관리 또는 미등록)

### nslgroup — 조 이름 형식
- GROUP_NM: "A조", "B조", ... "P조" (영문 알파벳)
- GROUP_CD: "1", "2", ... (숫자)

### tourplayer — 선수 ID
- id_no: KTTA 선수번호 (예: "202502007159", 12자리)
- KOR_NM: 팀명 (선수명이 아님!), TEAM_NM: 팀명
- 개인 선수가 아닌 **팀 단위**로 등록됨

### nslresult — 예선 결과
- ID_NO1_L/R: KTTA 선수번호
- SCORE_L/R: 세트 승수 ("0", "3" 등)
- WINNER_GB: "1"(좌승) 또는 "2"(우승)

### nstresult — 본선 결과
- RH_CD: 6자리 형식 "091001", "091002" (09=탁구, 10=32강(?), 01=경기번호)
- POSITION_L/R: 대진 번호

### nsddaejin — 본선 대진
- entrant_team_id: KTTA 선수번호
- SEED_NO: NULL (시드 미사용)

## 매핑 스크립트 (ktta_sync_v2.py)

### 지원 옵션
- --check: 데이터 생성 확인만
- --insert: DB에 삽입
- --clean: 삽입 데이터 삭제
- --local: 로컬 DB 대상 (기본: 원격)
- --verify: 원격 DB 기존 데이터 확인

### 생성 결과 (52회 기준)
| 테이블 | 우리 생성 | 원격 실제 |
|--------|----------|----------|
| tour | 1 | 1 |
| detail_class_cd | 9 | 8 |
| tourplayer | 361 | 886 |
| nslgroup | 109 | 93 |
| nslresult | 442 | 351 |
| nslresultdetail | 1,493 | 2,252 |
| nslresult_sum | 361 | 208 |
| nsddaejin | 220 | 238 |
| nstresult | 216 | 248 |
| nstresultdetail | 750 | 874 |

### 차이 원인
- tourplayer: 원격은 팀 단위 등록, 우리는 개인 선수
- nslresultdetail: 원격이 더 많음 (부전승 등 추가 기록)
- detail_class_cd: 원격에는 1-2학년/3학년 없음

## 다음 단계 (5/3 실시)
1. RH_CD 형식 맞추기 (6자리: 09XXYY)
2. KTTA 선수번호(ID_NO) 매핑 — tourplayer에서 조회하여 매칭
3. GROUP_NM 영문 알파벳 형식으로 변환
4. 대학연맹 데이터로 전체 프로세스 실전 테스트
