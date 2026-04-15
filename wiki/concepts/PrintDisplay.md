---
title: "PrintDisplay (인쇄 표시 개선)"
type: concept
tags: [print, css, bracket, scoresheet, flask, jinja2]
sources: [20260410-print-display]
last_updated: 2026-04-10
---

# PrintDisplay (인쇄 표시 개선)

인쇄용 출력물(대진표, 기록지)의 가독성을 높이기 위한 CSS/HTML 설계 패턴.

## 인쇄 템플릿 구조

Flask/Jinja2 기반 standalone HTML. 브라우저에서 `window.print()` 또는 직접 PDF 저장.

```
dist/templates/
  print_scoresheet.html      — 개인전 기록지
  print_league.html          — 리그 기록지
  print_bracket.html         — 본선 대진표
  print_team_scoresheet.html — 단체전 기록지
  stage2/scoresheet.html     — 2단계 기록지
```

## is32 플래그 패턴

`print_bracket.html`에서 대진표 크기에 따라 CSS 값을 동적 결정:

```jinja2
{% set is32 = (bracket_size == 32) %}
.box-name { font-size: {{ '9pt' if is32 else '13pt' }}; }
.box-team { font-size: {{ '7pt' if is32 else '9pt' }}; }
```

32인 대진표는 셀 높이가 약 21px(672px÷32)로 좁아 글자 크기를 더 작게 유지해야 한다.

## 두 줄 표시 레이아웃

개인전 대진표에서 선수명 + 학교명을 두 줄로 분리:

```css
.box {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.box-name { font-weight: bold; white-space: nowrap; text-overflow: ellipsis; }
.box-team { color: #555; white-space: nowrap; text-overflow: ellipsis; }
```

```html
<span class="box-name">{{ d.name }}</span>
<span class="box-team">{{ d.team }}</span>
```

단체전([[TeamMatch]])에서는 `gracket-lite.js`의 `.gk-name-wrap`(column flex)로 동일 효과 구현.

## 학교명 축약

단체전 대진표에서 긴 학교명 처리 (`dist/static/js/main.js`):

```js
function shortenSchoolName(name) {
  return name.replace('초등학교', '초').replace('중학교', '중').replace('고등학교', '고');
}
```

## 인쇄용 vs 화면용 CSS 구분

브라우저 화면용(`gracket-lite.css`)과 인쇄용 스타일은 분리 유지:
- 브라우저: `font-size: 12px / 10px`
- 인쇄용: pt 단위, 별도 조정

## 폰트 크기 변경 주의

CSS 폰트 크기 1~2pt 변경은 육안 식별 불가. 32인 대진표 기준 셀 높이 ≈ 16pt이므로 의미 있는 변경은 최소 3pt 이상이어야 한다.

## 관련 개념

- [[ScoreSheet]] — 기록지 인쇄 템플릿
- [[TournamentBracket]] — print_bracket.html 개인전 대진표
- [[TeamMatch]] — gracket-lite 단체전 대진표
- [[DeploymentArchitecture]] — gunicorn 재로드 절차
