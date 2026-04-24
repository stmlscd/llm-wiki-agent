# 2026-04-24 TT-Result 브랜드 실배포 (VPS + m4sellma)

## 배경
- 어제(04-23) HyperFrames 홍보 영상 히어로 프레임 기반 브랜드 스펙만 문서화
- 사용자가 "현 화면은 밋밋, 즉시 적용" 결정 → 대회 D-4 에도 불구하고 양쪽 서버 배포 결정

## 변경 파일
### 로컬(Windows F:) 커밋 대상
- `src/templates/index.html` — 브랜드 헤더(topbar + logo + net + nav) 추가, alpine.js 로직 보존
- `src/static/css/main.css` — CSS 변수 prelude + 헤더/메뉴 카드/모달 다크 브랜드 스킨
- `university-league/app/templates/index.html` — 동일 헤더 (8100 app 용)
- `university-league/static/css/main.css` — 동일 CSS (src 에서 복제)

### 배포 대상
- **m4sellma**: `/Volumes/Data/WWW/tt-result-src/` + `/Volumes/Data/WWW/university-league/` (8100 · 8101 · 8102 3 screen 재시작)
- **VPS**: `/var/www/tt-result/app/templates/index.html` + `/var/www/tt-result/static/css/main.css` (systemd tt-result 재시작)

## 배포 절차
1. 백업 (양쪽 서버에 `*.bak-YYYYMMDD_HHMMSS` 생성)
2. SFTP 업로드
3. 서비스 재시작 (m4sellma: screen detached, VPS: systemctl restart tt-result)
4. HTTP 200 + body 에 `brand-logo`·`class="tt"` 문자열 검증

## 결과
- m4sellma :8100 / :8102 — PASS
- VPS tt-result.sellma.kr (HTTPS) — PASS (200 + brand-logo + tt-span)
- VPS www.sellma.kr (HTTP) — PASS
- pytest 43/43 통과, 변경 파일 ruff clean

## 주의·안전 장치
- 기존 alpine.js `mainApp()` 로직, `x-model`·`@click` 이벤트, 메뉴 카드 4개 모두 기능 유지
- 색·레이아웃만 변경, 메뉴 이름은 한글 유지 (브랜드 네비게이션은 상단 장식용 영문)
- 백업 파일로 롤백 가능: `*.bak-YYYYMMDD_HHMMSS`
- 규칙 V1.1 §세로 배열 · §기권 취소선 유지
- 규칙 V1.1 과 충돌 없음 (대진표·기록지는 미변경)

## 향후 (05-03 이후 W1~W4)
- 신규 랜딩 개선 (배경 그래픽, 탁구 네트 SVG 정밀화)
- 내부 `tournament_52nd.html` · `tournament_univ.html` 헤더 브랜드 적용
- 관리 화면 변수 치환
- 메뉴 개편(역할 기반 — 현재 한글 4개 → TOURNAMENTS · PLAYERS · RESULTS · RANKINGS)

## 관련 문서
- `docs/design/tt-result-branding.md` (12섹션 스펙)
- `docs/design/assets/tt-result-brand-hero.jpg` (원본 히어로)
- `docs/post-tournament-tasks.md` P0-10 (W2~W4 추가 작업)
