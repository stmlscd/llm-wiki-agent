# 2026-04-26 운영 모델 확정 + 대학연맹전 장소 수정

## 1. 사건 시퀀스

### (가) 대학연맹전 장소 수정 (commit `ce3b255`)
- M1 Max Claude Desktop 이 brand `claude/init-project-repo-6ORWw` 에 1-커밋 PR 후보 생성
- 변경 내용: `PROJECT_BRIEF.md` L37 — "인천 계양체육관" → "**제천시 어울림체육관**"
- claude.ai/code 시도 시 API 전달 오류 5회 → 부분 산출물만 남음
- Windows PC Claude 가 main fast-forward 머지 + push + 브랜치 삭제

### (나) m4sellma `~/tt-result` 재생성
- 2026-04-25 git bundle 로 만들어둔 `~/tt-result/` 가 사라짐
- 사유 확인: M1 Max Claude Desktop 이 사용자에게 삭제 지시 → 사용자 실행
- Windows PC Claude 가 git bundle 로 재생성 (HEAD `ce3b255`)

### (다) 운영 모델 명문화
사용자가 의도된 운영 모델 설명:
- claude.ai/code (cloud Claude) 통합 관제가 장기 그림이었으나 API 버그로 보류
- 평시: Windows PC Claude 총괄
- 대회 D-0~D+4: MacBook Air (몽실) 로 사용자 직접 현장 대응
- 대회 후: Windows PC Claude 복귀

## 2. 운영 모델 (확정)

| 시점 | 머신 | 총괄 |
|------|------|------|
| 평시 (오늘 D-2 포함) | Windows PC | Claude (Windows PC 세션) |
| 대회 D-0~D+4 | MacBook Air (몽실) | 사용자 직접 |
| 대회 후 복귀 | Windows PC | Claude |
| 장기 (보류) | cloud Claude | 통합 관제 |

## 3. 디바이스 vs 서버 정리

| 이름 | 종류 | 역할 |
|------|------|------|
| Windows PC | 사용자 디바이스 | 메인 개발 |
| MacBook Air = 몽실 (M4 Mac) | 사용자 디바이스 | 대회 현장 대응 |
| M1 Mac (몽글) | 사용자 디바이스 | 보조, 텔레그램 |
| M1 Max Studio | 사용자 디바이스 | Cowork 분석 |
| **m4sellma** (Mac mini) | **서버** | 테스트 + 백업 (192.168.1.202) |
| **VPS** | **서버** | 운영 (115.68.177.35) |

⚠ MacBook Air ≠ m4sellma 서버 (이름 비슷, 별개 머신)

## 4. 다른 Claude 세션과의 충돌 처리 원칙

### 사례 (2026-04-26)
M1 Max Claude (cloud Claude 미래 그림 기준) ↔ Windows PC Claude (현재 fallback 기준) — 같은 m4sellma `~/tt-result/` 에 반대 방향 지시

### 원칙
1. 모든 Claude 세션은 `docs/operating-model.md` 우선 참조
2. m4sellma 등 공유 인프라 변경은 본 문서 갱신 후 행동
3. 대회 D-0~D+4 는 Claude 자율 결정 금지
4. cloud Claude 상태 변경 시 본 문서 §3 갱신

## 5. 변경 산출물

- `docs/operating-model.md` (신규 - 7섹션)
- `PROJECT_BRIEF.md` (장소 수정, ce3b255 — 이미 머지 완료)
- `memory/user_devices.md` (MacBook Air 역할 명확화)
- `memory/project_operating_model_20260426.md` (신규)
- llm-wiki: 본 raw + sources

## 6. m4sellma 동기화
- HEAD = `ce3b255` (2026-04-26 venue fix 까지 반영)
- 본 문서 + `docs/operating-model.md` push 후 git bundle 증분으로 m4sellma 에도 반영 예정
