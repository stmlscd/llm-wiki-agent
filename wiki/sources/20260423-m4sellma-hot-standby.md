---
title: "20260423 m4sellma 핫 스탠바이 전환 — VPS 독립 복제"
type: source
tags: [infrastructure, mysql, hot-standby, m4sellma, vps, replication]
date: 2026-04-23
source_file: raw/20260423_m4sellma_hot_standby.md
---

## Summary
m4sellma(Mac mini M4, 192.168.1.202) 를 VPS(115.68.177.35) 와 독립적으로 가동 가능한 완전 복제 서버로 재구성. 로컬 MySQL 9.6 설치 + 4 DB(tt_result·kuttf_db·kettf_db·ktta_db) 전체 복제 + 앱 .env APP_SSH_TUNNEL=false 전환 + screen-based uvicorn 기동. VPS 장애 시 m4sellma 단독 운영 가능.

## Key Claims
- 이전 구조(m4sellma 앱이 VPS MySQL 에 SSH 터널로 붙음)는 **"백업"의 의미가 없었다** — VPS 장애 시 같이 죽음
- macOS 의 TCC(Full Disk Access) 가 `/Volumes/Data` 의 venv/pyvenv.cfg 읽기를 막아 LaunchAgent 실패 → **screen detached session** 이 현실적 우회
- mysqldump 의 stderr 경고가 stdout 으로 파이프되면 덤프 파일 첫 줄에 섞여 import 가 SQL syntax error — `2>/dev/null` 로 분리 필수
- VPS 외부 공개 포트는 80·443·8843 만 NAT 매핑, 8100·8102 는 LAN 전용
- m4sellma 의 HTTPS(8843→8101) 는 .env 변경 후 재시작하지 않으면 handshake timeout → 3 screen 동시 재시작 규율 필요

## Connections
- [[ServerRules]] — 이 전환이 규칙 제정의 근거
- [[DeploymentArchitecture]] — 3-tier (개발·테스트·운영) 구조 보강
- [[Sellma]] — m4sellma 물리 자산
- [[BlueKiwi]] — WF:4 Step 7 리허설이 이 문제를 드러냄

## Contradictions
- 기존 [[DeploymentArchitecture]] 에서 m4sellma 를 "테스트 서버" 로만 기술 — 이번 전환 후 "백업+테스트" 로 재정의 필요
