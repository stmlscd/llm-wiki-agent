# mafia-codereview-harness 실물 CLAUDE.md 확인 후 §17 재평가

**일시**: 2026-04-24 저녁 (최초 분석 직후)
**소스**: `L:\TT-Result_Project\etc\CLAUDE.md` — 사용자가 이 plugin 의 실제 CLAUDE.md 를 확보해서 보여줌

## 실물 CLAUDE.md 핵심 내용

### 산출물 경로
`.review-artifacts/{branch-name}/` 하위에 저장 (branch 는 `git branch --show-current` 로 resolve):
- `design-intent.md` — 설계의도 문서
- `code-quality-guide.md` — 평가기준 문서
- `pr-body.md` — PR 본문
- `review-comments.md` — 리뷰 코멘트

### 참조 문서 (선행 작성 필요)
- `docs/code-convention.yaml` — 팀 코딩 컨벤션, `stacks` 필드로 스택별 필터링
- `docs/adr.yaml` — Architecture Decision Records, `stacks` / `date` / `status` 조회

### 파이프라인 규칙 5개
1. Fork(worktree) 로 실행하는 단계에서는 산출물만 생성, 소스 무수정 (리뷰 반영 단계 제외)
2. 평가기준 생성 시 ADR 전체를 포함하지 않고 관련 항목만 추출
3. 코드리뷰는 반드시 평가기준 근거 필수, 취향 리뷰 금지
4. 사용자 확인 요청 시 모호한 논의점 함께 정리하여 전달
5. base branch 기본값 = `main`

## 재평가 (어제 vs 오늘)

| 항목 | 어제 | 실물 반영 | 변경 사유 |
|------|------|----------|----------|
| 용도 적합성 | 중 | **중상** | YAML 기반 근거 리뷰는 BlueKiwi/gstack 에 없는 고유 가치 |
| 프로젝트 독립성 | 상 | **상** | `.review-artifacts/` 격리 + fork/worktree 소스 무수정 |
| 안정성 리스크 | 중 | **낮음** | 리뷰 단계 소스 미수정이 규칙화 |
| 대체 수단 | 낮은 필요성 | **중간 필요성** | "근거 없는 취향 리뷰 금지" 철학은 고유 |
| 유지 비용 | 중 | **중상** | `code-convention.yaml` + `adr.yaml` 작성·유지 선행 |
| 철회 경로 | 상 | **상** | `/plugin uninstall` + yaml 삭제 |

## 핵심 인사이트

1. **의외로 가벼움**: 산출물 4개 markdown + YAML 2개 참조. DB·외부 API 없음
2. **진입 장벽은 YAML**: plugin 가치 = `code-convention.yaml` + `adr.yaml` 품질에 전적 의존
3. **tt-result 에 ADR 문화 없음**: CLAUDE.md 에 산문으로만 규율 기록. YAML 전환이 선행 작업

## 수정된 결정

**대회 후 재검토 유지** — 단, 보류 사유 재정의:
- ❌ 이전 사유: "2 commit 신생 repo 유지보수 리스크"
- ✅ 실제 사유: "`docs/code-convention.yaml` + `docs/adr.yaml` 선행 작성이 필요한데, 대회 D-4 에 시작하면 본질 작업 시간 잠식"

## 재검토 시 선행 작업 순서 (2026-05-17 이후)

1. `docs/code-convention.yaml` 초안 — CLAUDE.md §3~14 를 스택별 구조화 (Python/JS/DB/API)
2. `docs/adr.yaml` 초안 — §16·17·18 을 ADR-001~003 으로 전환
3. `/plugin marketplace add vibemafiaclub/mafia-codereview-harness` + `/plugin install mafia-codereview`
4. 최근 PR 1건에 시범 리뷰 → BlueKiwi WF:4 Step 4~6 대비 고유가치 평가
5. 유지/폐기 최종 결정
