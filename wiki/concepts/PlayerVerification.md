---
title: "PlayerVerification (선수 검증 시스템)"
type: concept
tags: [verification, csv, player, activity-restriction, migration]
sources: [52nd-president-cup]
last_updated: 2026-04-10
---

# PlayerVerification (선수 검증 시스템)

참가 선수 명단과 초등부 선수 명부를 대조하여 활동제한·이적 선수를 자동으로 검출하는 시스템.

## 검사 현황 (2026-04-10 기준)

| 항목 | 수치 |
|------|------|
| 검사 인원 | 421명 |
| 활동제한 | 3명 |
| 명부 미등록 | 2명 |

## 데이터 소스

| 파일 | 내용 |
|------|------|
| `탁구 참가선수명단 엑셀.csv` | 참가 신청 선수 (422행) |
| `2026 초등부 선수 명부.csv` | 전체 등록 명부 (623행) |
| `2026 이적선수 및 활동제한.csv` | 이적·활동제한 선수 (46행) |

## CSV 파싱 주의사항

활동제한 CSV는 **섹션 헤더가 포함된 다단(multi-section) 구조**:
```
[섹션헤더 행]
컬럼1, 컬럼2, ...
데이터 행
...
[다음 섹션헤더]
```

- `csv.DictReader` 사용 **금지** — 섹션 헤더를 일반 행으로 읽어 오파싱 발생
- **올바른 방법**: `csv.reader` + 컬럼 인덱스 직접 참조

```python
# 올바른 예
with open(file) as f:
    reader = csv.reader(f)
    for row in reader:
        if is_section_header(row):
            continue
        name = row[2]  # 컬럼 인덱스로 접근
```

## API

```
GET /api/verification   → 검사 결과 대시보드
```

## 구현 파일

- `scripts/verify_players.py` — 검증 스크립트
- `dist/app.py` — `/api/verification` 라우트

## Connections

- [[52ndPresidentCup]] — 검증 대상 대회
- [[DeploymentArchitecture]] — 서버 기능으로 4단계 배포
- [[52nd-president-cup]] — 구현 소스
