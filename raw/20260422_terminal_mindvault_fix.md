# 2026-04-22 MindVault 훅 수정 + Windows 터미널 선택

## MindVault 훅 터미널 깜빡임 수정
- 원인: Windows에서 mindvault.exe 콘솔 프로그램 실행 시 터미널 창이 매번 생성/소멸
- 위치: `~/.claude/hooks/mindvault-hook.sh` 106행
- 해결: Python subprocess의 `creationflags=0x08000000` (CREATE_NO_WINDOW) 플래그 사용
- 훅 기능(MindVault 컨텍스트 주입)은 유지하면서 깜빡임만 제거

## Windows 터미널 에뮬레이터 조사
- 맥: Warp + iTerm2 + tmux 조합 사용 중
- Warp: Windows 미지원 (2026-04 기준, macOS/Linux만)
- 조사 대상: WezTerm, Ghostty, Alacritty, Windows Terminal, Kitty, Tabby

### 최종 선택: Alacritty 포터블
- Rust 기반 GPU 가속, 최고 속도
- tmux와 궁합 최고 (자체 분할 없음 = tmux에 위임)
- 포터블 버전으로 설치 부담 없음
- 미니멀 — 설정은 YAML 하나

## MCP 서버 상태
- Claude Code `/mcp` 확인 → 서버 미설정
- `/doctor` 진단 완료, 이상 없음
