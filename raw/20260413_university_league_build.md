# 2026-04-13: 대학연맹 시스템 구축 + DB 분리 + 엑셀 업로드

## 주요 작업

### 1. DB MySQL 통일 (SQLite 완전 제거)
- 모든 환경(Windows/Mac/VPS)에서 VPS MySQL 사용
- SSH 터널(paramiko)로 3306 포트 차단 우회
- tt_result DB에 VPS 전체 데이터 임포트 (447선수, 559결과, 262경기 등)

### 2. 52nd UI 이식 (종료 대회 표시 방식 확정)
- VPS 최신 정적 파일 다운로드: main.js(세트점수 표시), gracket-lite.js, CSS
- compat_52nd.py: 52nd Flask API와 동일 형식 15개 엔드포인트
- 핵심 수정: API 응답을 52nd와 완전 동일하게 (직접 배열 반환, 한글 키)
- 상장대장: loadContentForTab에서 loadVerification→loadAwards로 수정
- 대진표 인쇄: landscape→portrait 전환
- 읽기 전용: IS_ADMIN=false, 관리자 버튼/모달 숨김

### 3. 메인 메뉴 4개 구조 확정
- 대회 등록 🔒 → 새 대회 생성 폼
- 대회 관리 🔒 → 연맹 선택 → 대회 리스트 → 관리자 모드 (IS_ADMIN=true)
- 대회 기록 확인 → 공개 접근 → 읽기 전용 (IS_ADMIN=false)
- 관리자 등록 🔒 → superadmin 전용

### 4. DB 연맹별 분리
- kettf_db: 초등연맹 (52nd 대회 데이터 이관)
- kuttf_db: 대학탁구연맹
- ktta_db: 대한탁구협회
- tt_result: 시스템 DB (leagues, users, tournament_info)
- API가 세션의 league_code에 따라 해당 DB에 접속

### 5. 대학연맹 대회관리 시스템 구축
- tournament_univ.html 템플릿: 7개 탭 (단체전/개인전/개인복식/혼합복식/선발전/일정/검사결과)
- university-main.js: 대학연맹 전용 JS (탭 전환, API 호출, 대진표 렌더링)
- 단체전 경기방식: 4단1복 / 5단식 라디오 선택
- 추첨 입력: 수동입력 + OCR 판독 (UI 구현, OCR 백엔드는 다음 단계)

### 6. 엑셀 업로드 파서 구현
- upload.py 라우터: 대학연맹 참가신청명단 엑셀 자동 파싱
- 시트별 자동 감지: 단체전(남/여), 개인전(남/여), 개인복식(남/여), 혼합복식
- 파싱 결과: 단체전 118명, 개인전 124명, 복식 54조, 혼합 53조 → kuttf_db 저장
- 복식 저장: 선수1/선수2 형식으로 pair_name 생성
- 혼합복식: 다른 학교 간 조합 지원 (학교1/학교2 pair_team)

### 7. HTTPS 구성
- Mac mini self-signed SSL cert
- HTTP(8100) + HTTPS(8101) 동시 운영
- UniFi: 80→8100, 8843→8101

### 8. 밑그림 반영
- PDF: 메인화면 구조, 권한체계 (superadmin/admin/viewer)
- 엑셀: 연맹별 경기방식 (초등5단2복, 대학4단1복, 협회4단1복/5단식)
- 기록지 규칙: SINGLES/DOUBLES/MIXED/TEAM SCORE SHEET, 게임 수별 X 표시
- 세로 배열 필수: 탁구는 가로 배열 사용하지 않음

## 제44회 한국 대학탁구 연맹전 참가신청명단
- 7개 시트: 단체전(남10팀62명/여10팀56명), 개인전(남65명/여59명), 개인복식(남30조/여24조), 혼합복식(53조)
- 혼합복식 특이사항: 다른 학교 간 조합 가능 (경기대 남 + 용인대 여)
