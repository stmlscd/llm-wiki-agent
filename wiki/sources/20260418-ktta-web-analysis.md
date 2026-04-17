---
title: "KTTA 공식 웹사이트 분석 (ktt.or.kr / koreatabletennis.com)"
type: source
tags: [ktta, web-analysis, php, gnuboard, tournament, score-system]
date: 2026-04-18
source_file: web-crawl
---

## Summary

대한탁구협회(KTTA)가 운영하는 2개 웹사이트를 분석했다. 두 사이트는 동일한 PHP/그누보드 기반 시스템이며, 대회별로 분리 운영된다. 실시간 점수 입력 시스템이 AJAX 기반으로 구현되어 있고, jquery.gracket.js로 대진표를 렌더링한다.

## 사이트 비교

| 항목 | ktt.or.kr | koreatabletennis.com |
|------|-----------|---------------------|
| **대회** | 제72회 전국남녀종별탁구선수권대회 | 제78회 애경케미칼 전국남녀종합탁구선수권대회 |
| **competition_id** | 31, 32 | 2 |
| **점수 API** | `pcs_getscoredata.php` | `pcs_getgamedata.php` (메인) + `pcs_getscoredata.php` (모달) |
| **로그인 URL** | `/bbs/login.php?user=admin&flag=n` | 동일 |
| **로그인 API** | `/bbs/login_check_admin.php` | 동일 |
| **기본 테마** | theme06 | theme06 (동일) |
| **ID 기본값** | (빈값) | ktta |

## 기술 스택

- **백엔드**: PHP (그누보드 5 기반 CMS)
- **프론트엔드**: jQuery 1.12.4 + jQuery 3.3.1 (이중 로드), Bootstrap
- **대진표**: jquery.gracket.js v1.5.5
- **슬라이더**: Slick 1.8.1
- **DB**: MySQL (그누보드 표준)

## 인증 시스템

### 로그인 폼 구조
```
POST /bbs/login_check_admin.php
- url: 리다이렉트 URL (URL 인코딩)
- which_admin: "referee" | "ment" (심판/멘트자)
- mb_id: 사용자 ID
- mb_password: 비밀번호
- tcode: 대회코드 (L|YYYYMMDDHHMMSSNN 형식) 또는 토너먼트 코드
- table_no: 탁구대 번호 (숫자)
```

### 대회코드(tcode) 형식
- 리그: `L|2026030513551018` (L| 접두사)
- 토너먼트: `T|2024121520443193` (T| 접두사)
- 단독: `2025010811112223` (접두사 없음)

## 점수 입력 시스템

### 데이터 흐름
1. 대진표 페이지에서 경기 클릭 → Bootstrap 모달 열림
2. 모달 `shown.bs.modal` 이벤트 → `pcs_getscoredata.php` AJAX 호출
3. 응답 데이터로 점수 입력 폼 채움 (wr_1~wr_14, t_score, t_score2)
4. 메인 페이지는 60초마다 `pcs_getgamedata.php` 자동 폴링

### 점수 데이터 구조
```javascript
callbackGetScoreData(form, data) {
  // wr_1 ~ wr_14: 개별 포인트 (7세트 × 2선수 = 14)
  // t_score: 선수1 세트 스코어
  // t_score2: 선수2 세트 스코어
}
```

### 실시간 갱신 데이터 (callbackGetGameData)
```javascript
data[i] = {
  game_title,      // 경기명
  t_table_no,      // 탁구대 번호
  t_scorestr,      // 점수 문자열
  player1_win,     // 선수1 승리 표시
  player2_win,     // 선수2 승리 표시
  t_name,          // 선수1 이름
  t_name2,         // 선수2 이름
  t_score,         // 선수1 스코어
  t_score2,        // 선수2 스코어
  player1_str,     // 선수1 상세
  player2_str,     // 선수2 상세
  t_status         // 0=준비중, 1=경기중, 2=경기종료
}
```

## 대진표 데이터 구조

`win.tournamentData`로 JavaScript에 직접 임베드:

```javascript
win.tournamentData = [
  // 라운드별 배열
  [
    // 매치별 배열 (2선수)
    [
      {
        name: "선수명(소속)",
        id: "id-{tournament_id}-{seed}-{position}",
        seed: 1,
        score: "3",
        gameId: "game-{round}-{match}",
        nodeId: "node-{round}-{match}-{position}",
        t_status: "0|1|2",
        t_table_no: "2",
        teamNumber: "1",
        waitTxt: "",
        printConfirm: "Y"
      },
      { /* 상대 선수 */ }
    ]
  ]
]
```

## PHP 엔드포인트 목록

### 공통
| 경로 | 기능 |
|------|------|
| `/bbs/login.php` | 일반 로그인 |
| `/bbs/login.php?user=admin&flag=n` | 관리자(심판) 로그인 |
| `/bbs/login_check_admin.php` | 관리자 로그인 처리 (POST) |
| `/bbs/page.php?hid=tournament2` | 대진표 메인 (점수 입력 모달 포함) |
| `/bbs/page.php?hid=tournament_btype` | 대진표 B타입 (gracket 렌더링) |
| `/bbs/page.php?hid=tournament_result` | 대회 결과 페이지 |
| `/bbs/tournament_get_list.php` | 대진표 목록 AJAX (POST) |

### 점수 관련
| 경로 | 기능 |
|------|------|
| `pcs_getscoredata.php` | 개별 경기 점수 조회 (POST, JSON) |
| `pcs_getgamedata.php` | 전체 경기 현황 조회 (60초 폴링) |

### 출력/인쇄
| 경로 | 기능 |
|------|------|
| `/bbs/printtable.php` | 기록지 인쇄 (team_num, competition_id, tournament_id) |
| `/bbs/printtable_direct.php` | 기록지 직접 인쇄 (competition_id, gameId) |
| `/bbs/printtournamentalltable.php` | 전체 기록지 일괄 인쇄 (gangname, round) |
| `/bbs/cancelprinttable.php` | 기록지 인쇄 취소 |

### 데이터 다운로드
| 경로 | 기능 |
|------|------|
| `/bbs/excel_download_game_direct.php` | 개별 경기 엑셀 다운로드 |
| `/bbs/excel_download_allgame.php` | 전체 경기 엑셀 다운로드 |

### 게시판
| 경로 | 기능 |
|------|------|
| `/bbs/board.php?bo_table=notice` | 공지사항 |
| `/bbs/board.php?bo_table=cp_result` | 대회결과 |
| `/bbs/board.php?bo_table=cp_live` | 대회라이브 |
| `/bbs/board.php?bo_table=cp_current` | 실시간 대회현황 |
| `/bbs/tournament_video.php` | 경기 영상 |

## 연락처

- 대표: 02-420-4240
- 이메일: webmaster@koreatta.or.kr
- 주소: 서울 송파구 올림픽로 424 올림픽컨벤션센터 1층

## Connections

- [[52ndPresidentCup]] — 우리 시스템과 KTTA 시스템의 점수 입력 방식 비교 가능
- [[KTTADatabaseSchema]] — ngtos_tt DB와 이 웹 시스템의 관계
- [[TechStackMigration]] — PHP 그누보드 기반 vs FastAPI 전환

## Contradictions

- 없음 (신규 분석)

## Key Claims

- 두 사이트는 동일 코드베이스에서 대회별로 분리 운영 (theme06, 동일 JS)
- 점수 입력은 AJAX POST (pcs_getscoredata.php) 기반, 60초 폴링으로 실시간 갱신
- gracket.js를 사용하나 우리 시스템의 gracket-lite.js와는 다른 버전
- 대진표 데이터는 서버 사이드 렌더링으로 JS 변수에 직접 임베드
- 로그인 시 대회코드(tcode) + 탁구대번호(table_no) 필수 — 심판별 테이블 할당 구조
