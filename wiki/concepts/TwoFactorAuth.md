---
title: "TwoFactorAuth (2단계 인증 시스템)"
type: concept
tags: [auth, security, totp, telegram, jwt]
sources: [20260503-d1-session-migration-and-verification, 20260503-reconnect-fzip-github-sync]
last_updated: 2026-05-03
---

## 개요

tt-result 인증 시스템의 2FA 설계. OAuth-우선 방식을 폐기하고 비밀번호 + TOTP/Telegram 2FA 조합으로 확정 (2026-05-03 D+1).

## 채택 결정

| 라이브러리/방식 | 결정 | 이유 |
|---|---|---|
| pyotp (TOTP) | **채택** | 표준(RFC 6238), 의존 최소, Google Authenticator 호환 |
| Telegram Bot API | **채택** | 이미 watchdog 패턴 보유, urllib 직접 호출, 추가 의존 없음 |
| Authlib (OAuth) | 부채택 | OAuth 흐름 복잡성 대비 사용 가치 낮음 |
| fastapi-users | 부채택 | 통제력 상실, 우리 권한 모델과 맞지 않음 |
| Kakao 알림톡 OTP | 부채택 | 사업자 등록 부담 |

## 권한 매트릭스 (4역할)

| 역할 | 범위 | 비고 |
|------|------|------|
| superadmin | 전체 | 비상 백업 비번 별도 (APP_SUPERADMIN_EMERGENCY_*) |
| admin | league-scoped | 소속 연맹만 편집 가능 |
| operator | 대회 단위 | D+8~10 구현 예정 |
| viewer | 읽기 전용 | 공개 접근과 동일 수준 |

## 구현 파일

- `src/core/two_factor.py` — TOTP/Telegram 유틸
- `src/core/security.py` — 2FA dependency 주입
- `src/routes/auth.py` — 기존 인증 (확장)
- `src/routes/auth_2fa.py` — 7개 엔드포인트 (register/verify/disable TOTP·Telegram + status)
- `tests/test_two_factor.py` — 단위테스트 10/10 PASS

## Alembic 마이그레이션

`university-league/alembic/versions/20260503_auth_upgrade_2fa.py`
- users 테이블 +6 컬럼: hashed_password, totp_secret, totp_enabled, telegram_chat_id, telegram_enabled, failed_login_attempts
- audit_log 테이블 신규 생성

## 미완 (D+3~)

- TOTP/Telegram 등록 UI (템플릿)
- audit_log 조회 화면
- 역할 분리 UI
- VPS 배포

## Connections

- [[ServerRules]] — VPS 배포 전 m4sellma 검증 필수
- [[AdoptionPolicy]] — pyotp/Telegram 채택 시 §17 분석 완료
