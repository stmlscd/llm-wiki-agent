# 2026-04-26 오후 — 나노클로드(nanoclaw) 비서실장 도입 + 운영 위계 재편

## 1. 사건
사용자가 m4sellma 에 nanoclaw 를 Docker 로 설치 → tt-result 시스템의 **총괄 비서실장**으로 선정. Windows PC Claude(이 세션)는 **실행자**로 위치 변경.

## 2. 도입 배경
- claude.ai/code (cloud Claude) 통합 관제는 API 버그로 보류 (오늘 오전 기록)
- 그 대신 m4sellma 에 자체 비서실장 layer 를 Docker 로 띄워 업무 flow 원활화
- nanoclaw = openclaw (m4sellma `~/.openclaw/`, Ollama+gemma 테스트) 의 Claude Code 친화 변형

## 3. 새 위계 (2026-04-26 오후 확정)

```
┌─────────────────────────────────────────────────────────────┐
│  사용자                                                      │
└──────────────┬──────────────────┬───────────────────────────┘
               │                  │
               ▼                  ▼
       ┌──────────────┐   ┌──────────────────────┐
       │  나노클로드   │◀──┤ Windows PC Claude    │ (양방향 보고/조회)
       │  (총괄)       │   │  (실행)              │
       │  m4sellma     │──▶│                      │
       │  Docker       │   └──────────────────────┘
       └──────┬───────┘
              │ (현장·긴급 판단)
              ▼
      ┌────────────────────┐
      │  MacBook Air (몽실) │
      │  대회 D-0~D+4 현장  │
      └────────────────────┘
```

### 시점별 운영
| 시점 | 총괄 | 실행 |
|------|------|------|
| 평시 (오늘 D-2 포함) | 나노클로드 | Windows PC Claude |
| 대회 D-0~D+4 (2026-04-28~05-02) | 나노클로드 (현장 판단) | 사용자 직접 (MacBook Air) + Windows PC Claude (원격 지원) |
| 대회 후 복귀 | 나노클로드 | Windows PC Claude |
| (장기) | cloud Claude | 통합 관제 — **현재 보류** |

## 4. 미확정 사항 (TBD)

### 인터페이스 (나노클로드 → Windows PC Claude)
지시 전달 방식이 미정. 후보:
- (A) GitHub commits/issues — 나노클로드가 commit/issue push, Windows PC Claude 가 fetch + 실행
- (B) 텔레그램 — 봇 메시지로 양쪽 알림
- (C) 직접 SSH — 나노클로드가 m4sellma 에서 Windows PC Claude 의 작업 dir 제어
- (D) 별도 webhook / 메시지 큐

→ **첫 실 운영 시점에 확정 후 `docs/operating-model.md` 갱신**

### §17 정식 6항목 분석
- 도입 사실만 기록, 정식 분석은 대회 후 환류
- 사유: D-2 시점 분석 시간 확보 어려움 + 운영 흐름 안정화 후 평가가 정확

## 5. 자기 인식 (Windows PC Claude)
- 어제까지: "총괄"
- 오늘 오후 ~: "**실행자**" — 나노클로드 휘하
- 사용자 직접 지시도 그대로 수행 (나노클로드 우회 허용)
- 큰 결정은 가능한 한 나노클로드와 정합 확인 후 진행

## 6. 변경 산출물
- `docs/operating-model.md` §1 (위계 구조 신설), §2 (서버 + 나노클로드 항목 추가), §6 (현장 가이드 갱신)
- `memory/project_operating_model_20260426.md` (위계 + 자기 인식 추가)
- `memory/user_devices.md` (운영 모델 절 갱신)
- `memory/MEMORY.md` (인덱스 설명 갱신)
- llm-wiki: 본 raw + sources

## 7. 사용자 측 후속
- 사용자가 본 결정을 나노클로드에게 전달 예정 (대화 종료 시 사용자 메시지)
- 나노클로드 첫 지시 시점에 인터페이스 확정 → 본 문서 §4 갱신
