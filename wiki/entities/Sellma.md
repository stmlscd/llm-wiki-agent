---
title: "Sellma (sellma.kr 플랫폼)"
type: entity
tags: [platform, server, deployment, sync]
sources: [52nd-president-cup, 20260412-fastapi-migration-kickoff]
last_updated: 2026-04-12
---

# Sellma (sellma.kr 플랫폼)

탁구 대회 운영 시스템의 배포·동기화 인프라를 담당하는 플랫폼. 도메인 `sellma.kr` / `sellma.pe.kr` 운영.

## 구성 요소

### m4sellma (중앙 허브 서버)
- **역할**: 3대 PC(MacBook Air, MacBook Pro, Windows PC) 간 코드 동기화 허브
- **접속**: FTP 포트 822
- **경로**: `/Volumes/WorkSpace/project_src/claudespace/52nd_president_cup/`
- **동기화**: rsync 양방향 (작업 전 pull, 작업 후 push)

### community.sellma.kr (프로덕션 서버)
- **IP**: 115.68.177.35
- **OS**: Ubuntu 24.04 LTS
- **포트**: 8000 (gunicorn), Apache2 역방향 프록시 + SSL
- **DB**: MySQL 8.0
- **배포 경로**: `/var/www/kettf/2026/52nd/`
- **서비스**: systemd 자동 재시작

### sellma0080.pythonanywhere.com (구 공개 서버)
- **용도**: 1·2단계 예선 리그 공개 접속용
- **특징**: 로그인 불필요, 리그 결과 조회 전용

## 동기화 흐름

```
개발 PC (MacBook Air/Windows)
  ↓ rsync pull  ← m4sellma (허브)
  ↓ (개발·테스트)
  ↑ rsync push → m4sellma
  ↓ scp → community.sellma.kr (프로덕션)
```

### www.sellma.kr (Mac mini 테스트 서버, 2026-04-12~)
- **역할**: university-league FastAPI 테스트 서버
- **내부 IP**: 192.168.1.202:8100 (uvicorn)
- **외부 접속**: UniFi 포트포워딩 80→8100, 8843→8100
- **도메인 연결**: sellma.kr → DuckDNS(sellma0080) CNAME → KT DHCP IP
- **배포**: Windows PC에서 paramiko SFTP (scripts/deploy_to_mac.py)

## Connections

- [[52ndPresidentCup]] — 운영 대상 대회
- [[UniversityLeague]] — 새 테스트 서버 (www.sellma.kr)
- [[DeploymentArchitecture]] — 배포 상세 구조
- [[52nd-president-cup]] — 소스 프로젝트
