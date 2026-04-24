# 2026-04-24 mafia-codereview-harness plugin 도입 전 분석 → 대회 후 재검토 결정

## 배경
사용자가 YouTube 영상에서 본 Claude Code plugin `vibemafiaclub/mafia-codereview-barness` 를 설치 시도.
- 영상 표현: "AI 자율주행 — 목적지까지 자율 개발"
- SSH host key 이슈로 `/plugin marketplace add` 실패
- **§17 도입 전 분석 정책 첫 실전 적용**

## 조사 과정
1. WebFetch `vibemafiaclub/mafia-codereview-barness` → **404 Not Found**
2. 오타 추정 (barness → harness) → WebFetch `...-harness` → **README 획득**
3. WebSearch 로 동일/유사 도구 정황 파악

## 실체
- **6단계 코드리뷰 파이프라인**: 설계의도 → 평가기준 → PR 본문 → 리뷰 → 반영
- 외부 API 의존 없음, Git + Claude Code 기반
- MIT 라이선스
- 설정: `docs/code-convention.yaml`, `docs/adr.yaml` 커스터마이징
- **repo 상태: 단 2 commit** (신생)

## §17 6항목 분석

| 항목 | 평가 | 비고 |
|------|------|------|
| 용도 적합성 | 중 | BlueKiwi WF:4 Step 4~6, gstack /review, codex review 중복 |
| 프로젝트 독립성 | 상 | 로컬 plugin, 외부 API 없음 |
| 안정성 리스크 | 중 | 2 commit 신생 + 대회 D-4 |
| 대체 수단 | 낮은 필요성 | 기존 도구 80% 커버 |
| 유지 비용 | 중 | YAML 커스터마이징 |
| 철회 경로 | 상 | `/plugin uninstall` 간단 |

## "자율주행" 비유 실체
영상은 **마케팅 표현** — 실제는 PR 단위 리뷰 자동화 harness. 자율 개발 agent 가 아님.
기대치 과장 가능성을 사용자에게 전달.

## 결정
**대회 후 재검토** — 사용자와 합의. `docs/post-tournament-tasks.md P2-1` 등록.

### 재검토 시점
2026-05-17 (W3) 또는 BlueKiwi WF:4 Step 10 회고.

### 판정 지표
- repo commit 20+, star, 타 사용자 리뷰 축적
- 기존 도구(BlueKiwi + gstack + codex) 대비 고유 가치 확인

## 이 사례의 의의
**§17 도입 전 분석 정책이 처음으로 실전에서 작동한 사례**.
- 사용자가 직접 `/plugin marketplace add` 실행 → SSH 이슈로 실패
- Claude 가 §17 상기시키고 6항목 분석 수행
- 사용자와 함께 합리적 결정 (대회 후 재검토) 도달
- 분석 없이 설치했다면 대회 D-4 시점에 git 흐름 간섭 리스크 노출 가능

**정책의 가치 실증**: 신규 도구 설치 충동을 한 단계 걸러내는 관문으로 기능.

## 관련
- `docs/adoption-policy.md` — §17 정책 원본
- `docs/post-tournament-tasks.md P2-1` — 재검토 과제
- `memory/reference_mafia_codereview_harness.md` — 장기 기억
