# 20260411 대회 현장 작업 — 브래킷 버그 수정 & ngtos_tt DB 분석

**날짜**: 2026-04-11  
**장치**: Windows(현장 컴) + MacBook Air  
**작업자**: 이상철

---

## 작업 1: ngtos_tt DB 구조 분석 (Windows, 대회 현장)

KTTA 공식 대회운영시스템 DB(ngtos_tt) 분석. VPN으로 로컬 MariaDB(127.0.0.1:3307)에 접속.

### 생성 스크립트
- `scripts/dump_ngtos_schema.py` — 188개 테이블 스키마 + 3개 대회 샘플 데이터 → `docs/ngtos_db_schema.txt`
- `scripts/extract_relationships.py` — FK 관계 추출 (컬럼명 패턴 기반) → `docs/db_erd_data.json` (43개 핵심 테이블, 121개 관계)
- `scripts/check_db_api.py` — `result.sports.or.kr` API 엔드포인트 탐색

### 분석 대상 대회
| TO_CD | 대회명 |
|-------|--------|
| 202513537 | 제3회 유승민배 U12 전국 챔피언 탁구대회 |
| 202513027 | 제41회 교보생명컵 꿈나무 탁구대회 |
| 202614448 | 제52회 회장기 전국 초등학교 탁구대회 |

### 핵심 테이블 그룹 (7개 도메인)
| 도메인 | 주요 테이블 |
|--------|------------|
| 대회관리 | tour, tour_detail, detail_class_cd |
| 선수/팀 | player, person, teaminfo, tourplayer |
| 리그전 | nslgroup, nsldaejin, nslresult, nslresultdetail |
| 토너먼트 | nsddaejin, nstresult, nstresultdetail, nstresultrank |
| 경기일정 | matchschedule, matchheader |
| 실적/시상 | playerresult, teamresult, tourprise |
| 코드 | sido_cd, venue_cd |

### 목적
대학연맹 FastAPI 시스템 설계를 위한 공식 KTTA ngtos_tt DB 구조 사전 조사.

---

## 작업 2: ERD PDF 생성 (MacBook Air)

`docs/db_erd_data.json`(43 테이블, 121 관계)에서 graphviz ERD PDF 자동 생성.

### 생성 파일
- `docs/ngtos_erd_overview.pdf` — 전체 ERD 개요
- `docs/ngtos_erd_*.pdf` — 7개 도메인별 상세 ERD

### 기술 결정
- graphviz HTML-like 라벨: `<B>[PK]</B>`, `<FONT COLOR="#3498DB">[FK]</FONT>` 텍스트 태그 사용
- 이모지 사용 금지 — graphviz "fixed cell size with unspecified width or height" 오류 유발

---

## 작업 3: Windows ↔ Mac 실시간 동기화 설정

### 구성
- `scripts/sync_windows.sh` 작성 — 30분 간격 자동 루프
- Windows→Mac: `scp` (rsync 미설치 이유)
- Mac→m4sellma: `rsync -avz`
- 백업: `backup/sync_backup/*.tar.gz`, 최근 20개 유지

### 문제 해결
- `scripts/` 디렉토리 권한 `dr-x------` → `chmod 755`로 해결
- Windows SSH: OpenSSH Server 설치 필요 (`Add-WindowsCapability` + `Start-Service sshd`)
- rsync 없음 → scp pull 방식으로 전환

---

## 작업 4: 시상 탭 남자/여자단체 서브탭 제거

시상 탭의 남자단체/여자단체 서브탭 제거 (상장대장 인쇄에 이미 포함되어 중복).

### 변경
- `web/templates/templates/index.html`, `dist/templates/templates/index.html` 양쪽 수정
- `aw-tab-team-m`, `aw-tab-team-f` div 제거
- 기본 활성 탭을 `검사결과`로 변경

---

## 작업 5: 본선 대진표 오른쪽 결과 미표시 버그 수정 (핵심)

### 증상
`/print/bracket/<cat>` 페이지에서 왼쪽 절반 경기 결과는 표시되나 오른쪽 절반은 완전히 빈 상태.

### 원인 분석
`print_bracket.html` 오른쪽 연결선 루프의 매치 인덱스 계산 공식 오류:

```jinja2
{# 잘못된 공식 #}
{% set rm = half // (2 ** r) + mi + 1 %}
```

16인 브래킷(half=8) 기준 라운드별 실제 인덱스 vs 조회 인덱스:

| 라운드 | r | 실제 오른쪽 인덱스 | 구 공식 | 신 공식 |
|--------|---|-------------------|---------|---------|
| 16강 | 0 | 5,6,7,8 | 9,10,11,12 | 5,6,7,8 ✓ |
| 8강 | 1 | 3,4 | 5,6 | 3,4 ✓ |
| 준결승 | 2 | 2 | 3 | 2 ✓ |

```jinja2
{# 수정된 공식 #}
{% set rm = half // (2 ** (r + 1)) + mi + 1 %}
```

### 추가 발견: Flask 템플릿 경로 혼동

`dist/templates/templates/print_bracket.html`(수정)이 아닌 `dist/templates/print_bracket.html`(미수정)이 실제로 사용됨.

Flask 기본 템플릿 폴더 = `app.py` 위치의 `templates/` 서브폴더.
프로젝트에 `/templates/templates/` (구버전) vs `/templates/` (실사용) 이중 경로가 혼재.

VPS 실제 경로: `/var/www/kettf/2026/52nd/templates/print_bracket.html` — 별도 `scp` 배포 필요.

### 수정 파일 (4곳 모두)
- `dist/templates/print_bracket.html` ← 실사용
- `web/templates/print_bracket.html` ← 실사용
- `dist/templates/templates/print_bracket.html` ← 미사용(구버전)
- `web/templates/templates/print_bracket.html` ← 미사용(구버전)
- VPS: `/var/www/kettf/2026/52nd/templates/print_bracket.html` (scp 직접 배포)

### 주의: Windows 동기화로 덮어쓰기
최종 동기화 시 Windows 버전(구 공식)이 `dist/templates/`와 `web/templates/`를 덮어씀.
→ 동기화 후 즉시 재수정 + VPS 재배포 필요.
→ **향후**: 브래킷 공식 수정은 Windows 원본 파일에도 반드시 적용.

---

## 배포 구조 재확인

```
Windows(현장) → scp → MacBook Air(로컬) → rsync → m4sellma(백업)
                                         → scp  → VPS /var/www/kettf/2026/52nd/
```

- m4sellma rsync는 자동 동기화 용도
- VPS 배포는 **별도 scp** 필요 (rsync 경로 다름)
