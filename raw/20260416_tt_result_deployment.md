# 2026-04-16 tt-result.sellma.kr 배포 및 시스템 통합 작업 전체 기록

## 작업 배경

기존에 community.sellma.kr:8000 에서 Flask(gunicorn)로 운영하던 제52회 회장기 초등학교 탁구대회 시스템을, 새로운 통합 플랫폼 **tt-result.sellma.kr**로 이관하는 작업. tt-result는 FastAPI+uvicorn 기반이며, 초등연맹뿐 아니라 대학연맹·대한탁구협회까지 통합 관리하는 시스템.

---

## 1. MindVault 훅 정리

### 문제
Windows에서 Claude Code 사용 중 커맨드 창이 반복 깜빡거림.

### 원인
`~/.claude/settings.json`의 PostToolUse 훅 2개(mindvault-lore-hook.sh, mindvault-rules-hook.sh)가 Bash/Edit/Write 도구 실행마다 python3, mindvault.exe 등 콘솔 프로세스를 호출하여 Windows에서 cmd 창이 순간 생성/소멸.

### 해결
- PostToolUse 훅 2개 제거
- UserPromptSubmit 훅 1개(mindvault-hook.sh)만 유지 — 프롬프트 제출 시 1회만 실행

### 교훈
Windows 환경에서 빈번한 PostToolUse 훅은 콘솔 깜빡임을 유발하므로, 꼭 필요한 훅만 UserPromptSubmit에 배치.

---

## 2. 단체전 세트 점수 수정 및 검증 로직 추가

### 수정 1: 여자 32강 14번 (인천부평동초 vs 충북남평초)
- 1경기(김선율 vs 최라온) 3세트: 11:12 → 11:13 수정
- **중요**: 앱이 사용하는 DB는 `kettf_tournament`인데, 처음에 `kettf_db`에서 수정하여 반영 안 됨
- db.py의 DB_CONFIG를 확인하여 올바른 DB(kettf_tournament)에서 재수정

### 수정 2: 여자 결승 1번 (최선 vs 나연서)
- 3세트: 1:5 → 11:5 수정

### 검증 로직 추가
VPS routes_team.py에 `_validate_set_score()` 함수 추가:
- 11:0 ~ 11:9 → 정상
- 12:10, 13:11, 14:12 → 정상 (듀스 2점차)
- 11:10, 11:12, 14:13 → 차단 ("N세트 X:Y — 규칙 위반입니다.")
- 서버에서 세트 승수 재계산 (프론트엔드 값 불신뢰)

---

## 3. llm-wiki-agent 서브모듈 fork

- 원본 SamurAIGPT/llm-wiki-agent에는 push 권한 없음
- stmlscd/llm-wiki-agent로 fork 생성
- 서브모듈 remote URL 변경 후 push 성공
- 부모 레포(stmlscd/tt-result)도 서브모듈 참조 업데이트

---

## 4. tt-result.sellma.kr 신규 배포

### 인프라 구성

| 항목 | 설정 |
|------|------|
| DNS | tt-result.sellma.kr → 115.68.177.35 (A 레코드, 이미 설정됨) |
| SSL | Let's Encrypt certbot --apache (~2026-07-15, 자동 갱신) |
| 리버스 프록시 | Apache VirtualHost → 127.0.0.1:8100 (uvicorn) |
| HTTP | 301 → HTTPS 리다이렉트 |
| 서비스 | /etc/systemd/system/tt-result.service (uvicorn, 2 workers) |
| 소스 경로 | /var/www/tt-result/ |
| venv | /var/www/tt-result/venv/ (Python 3.12) |
| .env | APP_SSH_TUNNEL=false, DB 로컬 직접 연결 |

### 소스 이식 경로
m4sellma:/Volumes/Data/WWW/university-league/ → tar → 로컬 중계 → VPS:/var/www/tt-result/

### 의존성
FastAPI, uvicorn[standard], SQLAlchemy, Alembic, pydantic-settings, openpyxl, Jinja2, passlib[bcrypt], pymysql, cryptography, werkzeug, paramiko

---

## 5. DB 구조 및 데이터 이식

### DB 매핑
| 용도 | DB명 | 사용처 |
|------|------|--------|
| 시스템(기본) | tt_result | leagues, tournament_info, users |
| 초등연맹 | kettf_db | players, results, tiebreak_results, draw_results, tournament_matches, team_* |
| 대학연맹 | kuttf_db | 동일 구조 |
| 대한탁구협회 | ktta_db | 향후 사용 |
| 52nd 전용(구) | kettf_tournament | 8000포트 Flask 앱 전용, rk_* 랭킹 테이블 포함 |

### 초등연맹 데이터 정합성 확인 (kettf_db = kettf_tournament)
모든 테이블 행 수 일치: players(447), results(559), tiebreak_results(447), draw_results(272), tournament_matches(262), team_matches(62), team_game_details(350), team_players(387), team_draw(64)

### 대학연맹 데이터 선택적 이식
- **유지**: players(227명), draw_results(128), tournament_matches(87), leagues(3)
- **제거**: team_matches, team_draw, team_players, team_game_details (단체전 대진 + 임시기록)

### 주의사항
- **8000포트 Flask 앱은 `kettf_tournament` DB 사용** (db.py 확인 필수)
- **tt-result는 `kettf_db` 사용** (config.py league_databases 매핑)
- DB 수정 시 반드시 대상 DB 확인 필요

---

## 6. 초등연맹 랭킹 시스템 수정

### 문제점 (8000포트와 비교)
1. 순위: `rank_num` → JS는 `rank` 기대 → undefined
2. 학교: API가 `team` 반환 → JS는 `school` 기대
3. 대회 점수: player detail이 빈 배열 → scoreMap 생성 실패
4. 대통령기 컬럼 누락
5. 통합랭킹: 학년별 순위가 아닌 합계 점수 기준이어야 함

### 해결
- **ranking_compat.py**: `rank_num as rank`, `p.school` 직접 반환, `/player/{id}` 라우트를 `/{gender}/{grade}` 위에 배치 (라우트 충돌 방지)
- **player detail API**: `LEFT JOIN rk_tournaments`로 모든 대회 반환 (점수 없으면 0), `rating_class`, `tournament_name`, `code` 필드 추가, `total` 필드 추가
- **52nd-main.js**: codes 배열에 대통령기 추가, 컬럼 순서 회장기→교보→유승민→종별→대통령기→문체부, highlightCodes 회장기+종별만
- **통합랭킹**: `ORDER BY r.total_score DESC` + Python에서 동점 동순위 재계산
- **선수 상세 팝업**: `s.tournament_name`, `s.rating_class`, `data.total` 필드 매핑 수정

---

## 7. 초등연맹 개인전 수정 모달 추가

### 문제
tt-result의 match-modal이 뼈대만 있음 (입력 필드, 저장 버튼 없음)

### 해결
8000포트 index.html의 match-modal 전체를 tournament_52nd.html에 이식:
- 선수명/소속 표시, 1~5세트 점수 입력
- 게임 스코어 자동 계산, 사유 선택 (정상/기권/부전승/실격/경기포기)
- 기권 선수 선택 라디오 버튼
- 결과 저장 + 기록지 인쇄 버튼

---

## 8. 본선리그 기록지 버튼 복원

### 문제
52nd-main.js에서 bracket round 기록지 버튼이 의도적으로 제거됨 (`roundBtns.innerHTML = ''`)

### 해결
- roundOrder.forEach로 기록지 버튼 생성 코드 복원
- URL: `/t-scoresheet/` → `/print/tournament-scoresheet/` (tt-result 라우트에 맞게)
- renderPrintButtons의 IS_ADMIN 가드 제거 (모든 사용자 인쇄 가능)

---

## 9. 예선리그 관리자 기능 추가

### 추가된 버튼/패널 ({% if is_admin %})
- **순위 계산** — calcRankings() → POST /rankings/{category}/calculate
- **기권 선수 등록** — openForfeitPanel() → POST /api/forfeit (선수 삭제 + 시드 재정렬 + 결과 초기화)
- **선수 이동** — openMovePanel() → POST /api/move-player (조 간 이동 + 양쪽 결과 초기화)

### API 추가 (compat_52nd.py)
두 API 모두 SQLAlchemy 기반으로 새로 구현 (8000포트의 pymysql 코드를 FastAPI용으로 변환)

---

## 10. 대학연맹 기능 추가

### match-modal
초등연맹과 동일한 전체 입력 폼으로 교체

### 기권 선수 등록 (취소선 방식)
- 초등연맹과 다른 점: **선수를 삭제하지 않고 `is_forfeit` 플래그로 취소선 표시**
- DB: `ALTER TABLE kuttf_db.players ADD COLUMN is_forfeit TINYINT(1) DEFAULT 0`
- API: `POST /api/singles-forfeit` — action='forfeit' 또는 'restore'
- JS: `openForfeitPanel()` — Object.entries(groupData.players)로 dict 형태 순회
- UI: 기권 선수는 취소선+빨간 "기권" 표시, 복원 버튼으로 원래 상태 복구

### 기존 구현 확인
추첨 배정 / 본선 토너먼트 탭은 이미 구현되어 있었음 (loadSinglesDrawAssign, loadSinglesBracket)

---

## 캐시 관리
모든 JS/CSS 수정 시 템플릿에 `?v={timestamp}` 캐시 버스팅 적용.
브라우저 캐시로 인한 반영 안 되는 문제 반복 발생 — 수정 후 항상 캐시 버스팅 업데이트 필수.

---

## 현재 서비스 상태 (2026-04-16 기준)

| 서비스 | URL | 상태 |
|--------|-----|------|
| 52nd Flask (구) | community.sellma.kr:8000 | active (kettf-tournament.service) |
| tt-result (신) | tt-result.sellma.kr | active (tt-result.service) |
| Gnuboard | community.sellma.kr | active (Apache) |
| Django Blog | community.sellma.kr (nginx) | active |

### 다음 단계
- 대학연맹 대회: 2026-04-28 ~ 05-02 (D-12)
- VPS 배포 + 실전 운영 테스트 마무리
- 52nd 시스템은 tt-result로 완전 이관 후 kettf-tournament 서비스 종료 예정
