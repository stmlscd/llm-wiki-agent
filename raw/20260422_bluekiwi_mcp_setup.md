# 2026-04-22 BlueKiwi MCP 서버 설치 및 연결

## BlueKiwi 개요
- 오픈소스 워크플로우 에이전트 런타임
- GitHub: github.com/dandacompany/bluekiwi
- 작성자: Dante (datapod.k@gmail.com)
- 버전: 1.2.3 (npm 글로벌 설치)

## 설치 구성
- 프로필: tt-result
- 서버: http://127.0.0.1:3102
- 로컬 런타임: http://127.0.0.1:3103 (bundle 방식)
- SQLite: ~/.bluekiwi/quickstart/default/data/bluekiwi.sqlite
- 연동: Claude Code, Claude Desktop, Codex CLI, Gemini CLI, OpenClaw, Cursor, Antigravity, VS Code

## 설정 위치
- Claude Desktop: %APPDATA%/Claude/claude_desktop_config.json → mcpServers.bluekiwi
- Claude Code: .claude.json (프로젝트 로컬) → claude mcp add로 등록
- MCP 서버 스크립트: %APPDATA%/npm/node_modules/bluekiwi/dist/assets/mcp/server.js

## 문제 해결
1. 런타임 stopped → `bluekiwi start`
2. Claude Code MCP 미등록 → `claude mcp add bluekiwi -e BLUEKIWI_API_URL=... -e BLUEKIWI_API_KEY=... -- node <server.js>`
