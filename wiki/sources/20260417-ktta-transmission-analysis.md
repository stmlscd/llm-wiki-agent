---
title: "20260417 대한탁구협회 경기결과 전송 시스템 분석"
type: source
tags: [ktta, result-sports-or-kr, vpn, transmission, automation, video-analysis]
date: 2026-04-17
source_file: raw/20260417_ktta_transmission_analysis.md
---

## Summary

대한탁구협회 경기결과 전송 과정을 영상 3개(총 23.5분)에서 분석. KVPN 인증 → VPN(PPTP) → 대회운영 프로그램(tos09) → result.sports.or.kr 전송의 6단계 절차. 전송 시 20개 테이블(nsl*/nst*/nsd*)을 순차 전송. 대회코드 202614448. 자동화는 VPN 연결 후 로컬 DB(182.162.101.245:13834) 직접 삽입이 가장 현실적.

## Key Claims

- 2026년 3월부터 KVPN+VPN(PPTP) 연결이 필수로 변경됨
- 대회운영 프로그램은 Windows 데스크톱 앱, result.sports.or.kr에 연결
- 로컬 DB 서버 182.162.101.245:13834는 VPN 내부에서만 접근 가능
- 전송 테이블: nsl*(예선), nst*(본선), nsd*(대진) 계열 20개
- tt-result → KTTA DB 형식 변환 매퍼를 만들면 결과 입력(4단계)을 생략 가능

## Connections

- [[KTTA-TOS]] — 대회운영시스템 구조 및 접속 정보
- [[DeploymentArchitecture]] — VPN 내부 네트워크 추가 이해
- [[UniversityLeague]] — 대학연맹 대회도 동일 시스템으로 전송 예정

## Contradictions

- 없음
