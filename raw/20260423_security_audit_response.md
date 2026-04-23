# 2026-04-23 m4sellma 보안 점검 보고서 대응 + UniFi 포워딩 실측

## 배경
클로드 데스크탑 앱이 외부에서 m4sellma (www.sellma.kr) 에 대해 비파괴 정찰 기반 보안 점검 수행. P0 7건 + P1 3건 + 양호 5건 + 미확인 1건 보고서 작성. 파일: `~/Downloads/m4sellma_security_audit.md` (로컬, GitHub 미커밋).

## 대회 D-5 운영 영향 고려한 분류
즉시 처리 가능(리스크 낮고 이익 큰 것) 과 대회 후(5/3 Auth 업그레이드와 병행) 로 분리.

## 즉시 조치 완료 (커밋 b1ab744)

### P0-1: MySQL 외부 노출 차단
- 이전: `*:3306`, `*:33060` listen
- `/opt/homebrew/etc/my.cnf` 신규 작성: `bind-address = 127.0.0.1` + `mysqlx-bind-address = 127.0.0.1`
- `brew services restart mysql` → 127.0.0.1 만 listen 확인
- 로컬 앱(tt-result-src 8102, university-league 8100) 정상 연결 유지

### P0-2: Ollama 외부 노출 차단
- 이전: `*:11434` (Ollama.app GUI 기동)
- GUI app 완전 종료 + LaunchAgent `~/Library/LaunchAgents/com.sellma.ollama.plist` 배포
- `OLLAMA_HOST=127.0.0.1:11434` 환경변수 포함 CLI daemon
- launchctl bootstrap 후 127.0.0.1:11434 로만 listen
- 주의: Ollama GUI 앱을 재실행하면 `*:11434` 로 복귀 가능 → GUI 사용 자제 권장

### P0-6: FastAPI /docs·/redoc·/openapi.json 외부 노출 차단
- `src/main.py` + `university-league/app/main.py` 양쪽에 `APP_ENV` 분기 추가
- `APP_ENV=production` 일 때 `docs_url=None`, `redoc_url=None`, `openapi_url=None`
- 3개 screen 세션(8100/8101/8102) 을 `APP_ENV=production` 으로 재기동
- 외부 검증 (www.sellma.kr):
  - /docs /redoc /openapi.json 전부 404 ✅
  - /health 200 ✅
  - /.env /.git/config /static 404 ✅

## 사용자 선택으로 유지
- P0-3 SMB(445): 외부 유지 (파일공유 활용)
- P0-4 VNC(5900): 지금 유지, 대회 후 재검토

## 대회 후 처리 예정 (post-tournament-tasks.md)
- P0-5 macOS Application Firewall 활성화
- P0-7 Let's Encrypt 인증서 (self-signed → 공식 cert)
- P1-1 nginx 리버스 프록시 (도입 전 분석 정책 §17 대상)
- P1-2 보안 헤더 (HSTS/CSP 등) — nginx 도입과 함께

## UniFi 라우터 실측 (커밋 0c8b266)
사용자가 UniFi GUI 스크린샷 제공 → 실제 외부 공개 상태 확정.

### m4sellma 로 향하는 규칙
| 외부 | 내부 | 라벨 | 상태 |
|------|-----|------|------|
| WAN:80 | :8100 | www-sellma-http | ✅ 의도 |
| WAN:8843 | :8101 | www-sellma-https-alt | ✅ 의도 |
| WAN:822 | :22 | 822 | ⚠️ SSH 외부 공개 |
| WAN:2222 | :2222 | M4sellma | 💀 dead rule (listener 없음) |
| WAN:8888 | :8888 | test | 💀 dead rule |

### 보고서 P0 재평가
- P0-1~P0-4 (MySQL/Ollama/SMB/VNC) 는 **라우터에 포워딩 규칙 없음** → 원래부터 외부 노출 아니었음
- 보고서가 가정한 "방화벽 꺼져있으니 다 열려있다" 는 라우터 차단을 고려하지 않은 시나리오
- 오늘 적용한 127.0.0.1 바인딩은 **defense-in-depth** 관점에서 여전히 유효

### 새로 발견
- **WAN:822 SSH 공개** — 브루트포스 표적 → post-tournament-tasks P0-8 신규
- **WAN:2222 / WAN:8888** — dead rules → post-tournament-tasks P0-9 신규 (즉시 삭제 가능)

## 사용자 수동 조치 완료
- UniFi 포트포워딩에서 `M4sellma (WAN:2222)`, `test (WAN:8888)` 규칙 삭제 확인
- 외부 smoke: 2222·8888 connection refused ✅

## 남은 이슈
- **8843 HTTPS SSL handshake EOF** — Python 3.9 ssl + self-signed cert + uvicorn 조합의 고질 문제. 오늘 조치 탓 아님. Let's Encrypt 적용(P0-7) 또는 nginx(P1-1) 로 해결 예정.
- 대회 중 외부 접속은 HTTP 80 (`http://www.sellma.kr/`) 을 기본으로 사용하면 안정적.

## 관련 커밋
- `b1ab744` — security(m4sellma): 외부 공격 표면 축소 (MySQL/Ollama/docs)
- `0c8b266` — docs(security): UniFi 포워딩 실측 반영
