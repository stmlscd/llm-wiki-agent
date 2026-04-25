# M1 Max Cowork 프레임워크 도입 판단 + m4sellma git baseline 확립

**일시**: 2026-04-24 ~ 2026-04-25
**환경**: M1 Max (Cowork 분석) + Windows PC (메인 동기화) + m4sellma (git baseline 확립)

## 1. M1 Max Cowork 작업 (2026-04-24~25)

### 본편: `docs/framework-adoption-judgment-2026-04-24.md` (189줄, commit `9091b04`)
- 입력: Pulumi 블로그 PDF + 업로드 4개 파일 (code-convention.yaml + adr.yaml + Code Review Harness CLAUDE.md)
- §17 도입 전 분석 정책 6항목 적용
- 결정: **대회 전 새로 도입할 것 없음**

| 대상 | 결정 | 근거 |
|------|------|------|
| Superpowers (TDD) | Phase C 검토 | 방향성 정확, 회귀 테스트 박제 선행 필요 |
| GSD | 반려 | 기억 DB v1.0 더 적합 |
| GSTACK 23 역할 | 반려 | 단일 개발자 과대 (PDF도 자인) |
| 업로드 yaml 2개 | 폐기 (구조만 참고) | TS/Next.js/NestJS 스택 불일치 |
| mafia-codereview | 2026-05-17 재검토 (기 결정) | yaml 선행 작성 필요 |

### Phase A/B/C 시간축
- **A (D-4~D+4)**: 새 도구 금지, 대회 안정화
- **B (D+1~D+14)**: `tests/regression/` 박제 — 대회 A형 버그 → pytest
- **C (D+15~)**: code-convention.yaml + adr.yaml 신규 작성, plugin 재검토

### 부록: `docs/framework-adoption-judgment-2026-04-25-addendum.md` (179줄, commit `4aef1d8`)
- 입력: GSTACK 영상 (Garry Tan, 22:13) + OMX 영상 (허예찬, 29:10)
- 트랜스크립션 환경 미비 → 공개 저장소 기반 간접 분석

| 새 후보 | 결정 |
|---------|------|
| Playwright 실브라우저 QA | **Phase C 채택** — 4형(Output) 버그 잡기, 시나리오 5종 |
| Claude Code 훅 규율 확장 | **Phase C 채택** — PreToolUse + PostToolUse 5종 |
| OMX 자체 도입 | 반려 (Codex 전용) |
| GSTACK 23 역할 전체 | 본편 결론 유지 |

## 2. m4sellma git baseline 확립 (2026-04-25)

### 문제 상황
- M1 Max 에서 m4sellma 로 GitHub 통한 git pull 시도 → 실패
- m4sellma 에 GitHub 인증 미설정 (HTTPS PAT 없음, SSH key 미등록)
- 대회 D-3 시점 → 인증 셋업 자체가 §17 위반 리스크

### 해결: git bundle 우회
```bash
# Windows PC
git bundle create /tmp/tt-result.bundle --all  # 22MB

# paramiko sftp 로 m4sellma:/Users/m4sellma/tt-result.bundle 업로드

# m4sellma
git clone ~/tt-result.bundle ~/tt-result
git -C ~/tt-result remote set-url origin https://github.com/stmlscd/tt-result.git
```

### 검증
- HEAD = `4aef1d8` (Windows PC main 과 일치)
- docs/ 39개, 새 framework-adoption-judgment-* 2개 모두 존재
- 77M, 정상 working tree clean

### m4sellma 경로 정리
| 경로 | 역할 | git? |
|------|------|------|
| `~/tt-result/` | **git baseline (NEW)** | ✅ |
| `/Volumes/Data/WWW/tt-result-src/` | 8102 런타임 | ❌ (rsync 배포) |
| `/Volumes/Data/WWW/university-league/` | 8100/8101 런타임 | ❌ (rsync 배포) |

### 대회 후 정식 셋업 (Phase B)
- ssh-keygen + GitHub SSH key 등록 → `origin = git@github.com:...` → `git pull` 자동화

## 3. 정합성 확인
- 본편 §2-3 mafia-codereview 평가 = 어제 Windows PC 의 실물 CLAUDE.md 재평가와 **완전 일치**
  - 안정성 리스크 낮음 / 유지비용 중상 / yaml 선행 작성 필요
- 두 머신 독립 분석에서 동일 결론 → §17 정책 일관성 입증

## 4. 다음 행동
- D-3 현재: Phase A 그대로 (새 도구 금지)
- 2026-05-17: Phase C 진입 시 본편 + 부록 + reference_mafia_codereview_harness 동시 참조
- m4sellma 동기화 필요 시 git bundle 방식 (이번 사례 참고)
