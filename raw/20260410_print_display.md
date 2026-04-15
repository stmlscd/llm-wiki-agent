# 52nd 회장기 대진표/기록지 출력 표시 개선 작업 (2026-04-10)

## 작업 개요

제52회 회장기 전국 초등학교 탁구대회 운영 시스템에서 인쇄 관련 UI/출력물 개선 작업.

## 주요 변경 사항

### 1. 인쇄용 기록지/스코어시트 글자 크기 16pt 통일

대상 파일:
- `dist/templates/print_scoresheet.html` — `.name-cell` 28pt → 16pt
- `dist/templates/print_league.html` — `.rr-name`, `.rr-team`, `.match-name`, `.match-team` → 16pt
- `dist/templates/print_bracket.html` — `.box` 동적값 → 16pt (후에 다시 수정)
- `dist/templates/print_team_scoresheet.html` — 선수명/팀명 셀 → 16pt
- `dist/templates/stage2/scoresheet.html` — `.name-cell` → 16pt

### 2. 개인전 대진표 글자 크기 조정

`print_bracket.html` `.box` font-size:
- 32인 대진표: 16pt → 12pt (사용자 요청) → 최종 두줄 표시로 변경 시 9pt(이름)/7pt(학교)
- 16인 대진표: 16pt 유지 → 최종 두줄 표시로 변경 시 13pt(이름)/9pt(학교)

**교훈:** 1pt 차이(15pt→16pt)는 육안 식별 불가. 시각적으로 의미 있는 변경이 필요.

### 3. 개인전 대진표 두 줄 표시 (이름 + 학교명)

`print_bracket.html` 변경:
- `.box`에 `flex-direction: column` 추가, `white-space: nowrap` 제거
- `.box-name` 클래스: bold, 32인→9pt / 16인→13pt
- `.box-team` 클래스: 회색(#555), 32인→7pt / 16인→9pt
- HTML: 단일 span → name span + team span 분리

### 4. 단체전 대진표 학교이름 축약 (gracket-lite)

`dist/static/js/main.js` 변경:
- `shortenSchoolName()` 함수 추가
  - 초등학교 → 초
  - 중학교 → 중
  - 고등학교 → 고
- `renderTeamBracketTree()`에서 모든 팀명에 적용

### 5. 단체전 대진표 두 줄 자동 줄바꿈

`dist/static/js/gracket-lite.js` 변경:
- `.gk-name-wrap` div 래퍼 추가 (flex-direction: column)
- `.gk-name`, `.gk-team-label`에 `white-space: normal; word-break: break-word` 적용

`dist/static/css/gracket-lite.css` 변경:
- `.gk-name-wrap` 신규 추가
- 브라우저 화면용 글자 크기는 12px/10px 유지 (인쇄용과 구분)

## 배포

모든 변경사항 가상서버(115.68.177.35) `/var/www/kettf/2026/52nd/` 배포 완료.
배포 후 `kill -HUP $(pgrep -f 'gunicorn.*wsgi:app' | head -1)` 으로 gunicorn 재로드.

## 기술 스택

- Flask/Jinja2 템플릿 (인쇄용 standalone HTML)
- GracketLite.js (커스텀 브라우저용 대진표 렌더러)
- gunicorn (production WSGI server)
- 배포 경로: dist/ → 가상서버, web/ → 개발 병렬 관리
