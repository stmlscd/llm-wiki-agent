# 2026-04-13: Phase 3 UI 이식 + DB MySQL 통일 + HTTPS 구성

## 주요 작업

### 1. DB MySQL 완전 통일
- SQLite 제거, 모든 환경에서 VPS MySQL(tt_result DB) 사용
- Windows/Mac → SSH 터널(paramiko) → VPS MySQL:3306
- VPS 데이터 전량 임포트: 447선수, 559결과, 262경기, 387단체선수, 350게임상세

### 2. 52nd UI 이식 (종료 대회 표시 방식 확정)
- VPS의 최신 정적 파일 다운로드: main.js, gracket-lite.js(세트점수 표시 버전), main.css, print.css
- tournament_52nd.html 템플릿: 52nd index.html 기반, 관리자 기능 제거
- compat_52nd.py 라우터: 52nd Flask API와 동일 형식 15개 엔드포인트
- 방향 확정: 종료된 대회는 52nd UI를 읽기 전용으로 그대로 제공

### 3. API 호환성 수정
- /api/category/{cat}: 직접 배열 반환 (기존 {groups:[...]} → [...])
- /api/draw-info/{cat}: existing_draw, first_slots, bye_slots, bye_count 추가
- /bracket/{cat}/data: bracket → bracket_data, round_order 추가
- /api/awards: champion/runner_up → 우승/준우승/동메달3위 (한글 키)
- /api/team/bracket/{gender}: 동일 bracket_data 형식
- /api/team/draw-info/{gender}: existing_draw, bye_slots 추가

### 4. HTTPS 구성
- Mac mini self-signed SSL cert 생성 (certs/key.pem, cert.pem)
- HTTP(port 8100) + HTTPS(port 8101) 동시 운영
- UniFi 포트포워딩: 80→8100(HTTP), 8843→8101(HTTPS)
- 자체 서명 인증서 경고는 "계속하기"로 진행 가능

### 5. 밑그림 반영
- PDF 5페이지: 메인화면(대회등록/기록확인/관리자등록), 권한체계(superadmin/admin/viewer)
- 엑셀 3시트: 연맹별 경기방식(초등5단2복, 대학4단1복, 협회4단1복/5단식)
- OCR 대진표 입력: 현장 추첨 사진 → 자동 판독 기능 계획에 추가
- 기록지 규칙: SINGLES/DOUBLES/MIXED/TEAM SCORE SHEET, 게임 수별 X 표시
