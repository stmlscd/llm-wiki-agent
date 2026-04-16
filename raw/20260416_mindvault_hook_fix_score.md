# 2026-04-16 MindVault 훅 정리 + 단체전 점수 수정 + 서버 검증 로직 추가

## 1. MindVault PostToolUse 훅 제거

### 문제
- Windows에서 Claude Code 작업 중 커맨드 창이 반복 깜빡거림
- 원인: `~/.claude/settings.json`의 PostToolUse 훅 3개가 Bash/Edit/Write마다 python3, mindvault.exe 등 콘솔 프로세스 호출

### 해결
- PostToolUse 훅 2개 제거 (mindvault-lore-hook.sh, mindvault-rules-hook.sh)
- UserPromptSubmit 훅 1개만 유지 (mindvault-hook.sh) — 프롬프트 제출 시 1회만 실행

### 교훈
- Windows에서 빈번한 PostToolUse 훅은 콘솔 창 깜빡임을 유발
- 꼭 필요한 훅만 UserPromptSubmit에 배치

## 2. 단체전 여자 32강 점수 수정

### 경기 정보
- 여자/32강/14번: 인천부평동초등학교 vs 충북 남평초등학교
- 1경기(단식): 김선율 vs 최라온
- 3세트 점수: 11:12 → 11:13 수정

### 문제 과정
1. 처음 `kettf_db`에서 수정 → 반영 안됨
2. 앱이 실제 사용하는 DB는 `kettf_tournament` (db.py에서 확인)
3. `kettf_tournament.team_game_details` id=155에서 수정 → 정상 반영

### 교훈
- VPS의 Flask 앱은 `kettf_tournament` DB 사용 (db.py 참조)
- `kettf_db`는 별도 DB — 앱과 무관
- DB 수정 전 반드시 db.py의 DB_CONFIG 확인 필요

## 3. 서버 점수 검증 로직 추가

### 추가된 코드
- `_validate_set_score(s1, s2, set_num)` 함수 — routes_team.py에 추가
- 탁구 11점제 + 듀스(2점차) 규칙 검증
- 위반 시 "N세트 X:Y — 규칙 위반입니다." 메시지 반환 (400 에러)
- `save_team_match_result()`에서 저장 전 검증 + 서버측 세트 승수 재계산

### 유효한 점수 예시
- 11:0 ~ 11:9 (정상)
- 12:10, 13:11, 14:12 (듀스 정상)

### 차단되는 점수 예시  
- 11:10 (듀스 진입해야 함)
- 11:12, 14:13 (듀스에서 1점차)
- 15:11 (듀스에서 2점차 초과)

## 4. llm-wiki-agent 서브모듈 fork
- 원본 SamurAIGPT/llm-wiki-agent → stmlscd/llm-wiki-agent fork
- remote URL 변경 후 push 성공
- 부모 레포(tt-result)도 서브모듈 참조 업데이트
