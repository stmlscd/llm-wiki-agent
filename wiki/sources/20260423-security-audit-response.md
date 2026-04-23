---
title: "20260423 m4sellma 보안 점검 보고서 대응 + UniFi 포워딩 실측"
type: source
tags: [security, audit, mysql, ollama, fastapi, unifi, port-forwarding, defense-in-depth]
date: 2026-04-23
source_file: raw/20260423_security_audit_response.md
---

## Summary
클로드 데스크탑이 수행한 m4sellma 외부 보안 점검 보고서 P0 7건 중 3건(MySQL localhost, Ollama LaunchAgent, FastAPI docs 비활성) 즉시 반영. UniFi 포트포워딩 실측으로 P0-1~P0-4 가 라우터 차단 덕에 원래부터 외부 노출 아니었음을 확정. Dead rule 2건(WAN:2222, WAN:8888) 사용자 GUI 삭제 완료. 8843 HTTPS SSL EOF 는 기존 이슈로 대회 후 Let's Encrypt 로 해결 예정.

## Key Claims
- 보안 점검 보고서가 전제한 "방화벽 꺼져있음=외부 노출" 가정은 라우터 NAT 차단을 고려하지 않음 → 실측에서 다수 재분류
- `/opt/homebrew/etc/my.cnf` 의 `bind-address=127.0.0.1` + `mysqlx-bind-address=127.0.0.1` 은 MySQL 9.6 에서 X Protocol(33060) 까지 localhost 제한 가능
- Ollama macOS app 의 `launchctl setenv OLLAMA_HOST` 는 GUI 프로세스에 적용 안 됨 → LaunchAgent plist 로 CLI daemon 을 직접 관리해야 함
- FastAPI `docs_url=None` 환경변수 분기(`APP_ENV=production`) 는 개발 편의 유지 + 운영 공격 표면 축소에 효과적
- 자체 서명 cert + Python 3.9 ssl 모듈 조합의 uvicorn HTTPS 는 일부 클라이언트에서 handshake EOF — 자체 서명 인증서의 한계. Let's Encrypt 로 해결 가능

## Connections
- [[ServerRules]] — §6-a 에 조치 내역 반영
- [[AdoptionPolicy]] — nginx / fail2ban 향후 도입 시 §17 대상
- [[DeploymentArchitecture]] — m4sellma 외부 공개 실상 업데이트 (80·822·8843 만)
- [[Sellma]] — UniFi 라우터 운영 주체

## Contradictions
- 보안 점검 보고서 P0-1~P0-4 의 "Critical" 판정과 실측 차이 — 모순이라기보단 **정찰 범위 한계** (라우터 설정 미포함). 방어 심화(defense-in-depth) 관점에선 여전히 조치 가치 있음.
