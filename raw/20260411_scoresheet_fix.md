# 단체전 기록지 교보생명컵 기준 수정 & 16강 game_format 버그 수정

날짜: 2026-04-11 (오후)
장치: macbook_air

---

## 1. 16강 team_matches game_format 버그 수정

### 증상
16강 단체전 기록지에서 3·4번 경기(복식)에 DOUBLE 레이블이 표시되지 않음.

### 원인
16강 매치는 32강 승자 진출 자동 생성 로직으로 생성되는데, 이때 `game_format` 컬럼의 기본값이 `'단식,단식,단식,단식,단식,단식,단식'`(전부 단식)으로 세팅됨. 32강 실제 경기는 수동 입력 시 올바른 포맷이 지정되었으나 16강은 누락.

### 수정
```sql
UPDATE team_matches SET game_format='단식,단식,복식,복식,단식,단식,단식' WHERE round_name='16강';
```
→ VPS SQLite DB 16개 레코드 수정 완료

### 교훈
자동 진출 매치 생성 시 `game_format` 기본값을 연맹 표준(단단복복단단단)으로 설정해야 함. 현재 코드는 기본값이 전부 단식이므로, 향후 라운드 자동 생성 로직 개선 필요.

---

## 2. 대진표 오전/오후 차이 원인 조사

### 경위
사용자가 오전 출력 기록지와 오후 출력 기록지의 대진표가 다르다고 보고.

### 조사 결과
- 4월10일 VPS 백업(`/tmp/backup_apr10`) vs 현재 VPS DB 비교
- `team_draw` 테이블 데이터 완전 동일 → 시드 배치 변경 없음
- `team_matches` 데이터도 동일 (game_format 제외)

### 결론
오전/오후 차이는 `print_bracket.html` 오른쪽 절반 매치 인덱스 공식 버그 수정(2026-04-11 오전 작업)이 반영된 것. 시드/데이터 변경 아님.

---

## 3. 단체전 기록지 교보생명컵 기준 전면 수정

### 기준
제41회 교보생명컵 꿈나무 탁구대회 공식 TEAM SCORE SHEET

### 문제점 (수정 전)
1. Signature Umpires 행이 2페이지로 넘어가는 현상 (이전 세션에서 부분 수정했으나 완전하지 않음)
2. 1st~5th 세트 구분 가로줄 없음
3. Winner/Signature가 오른쪽으로 분리 렌더링됨 (`bl-body-td` 별도 tbody 구조 문제)
4. "Colour of playing shirt" 헤더가 1줄
5. 선수명과 세트 승수박스가 분리된 셀에 있음

### 수정 내용

#### 3-1. 4월10일 백업본으로 구조 복원
- 경기당 1개 `<tr>` + div 스택 방식 → 경기당 5개 sub-row (`height: 5.5mm`) 방식
- 각 세트행이 별도 `<tr>`이므로 자연스러운 가로 구분선 생김

#### 3-2. "Colour of playing shirt" 2줄
```html
<th class="bd-l" colspan="5">Colour of<br>playing shirt</th>
<th class="bd-r" colspan="5">Colour of<br>playing shirt</th>
```

#### 3-3. 선수명 + 승수박스 rowspan=5 단일 셀 통합
- 변경 전: 선수명 `rowspan=3` + 4th/5th 행에 승수박스 별도 `td colspan=5 rowspan=2`
- 변경 후: `rowspan=5` 단일 셀 안에 선수명(위) + 승수박스(아래) 통합

```html
<td colspan="5" rowspan="5" style="vertical-align:middle;...">
  <div style="font-size:16pt;font-weight:bold;margin-bottom:2mm;">{{ g.player1_name }}</div>
  <div><div style="display:inline-block;border:1.5px solid #000;min-width:11mm;...">{{ ns.w1 }}</div></div>
</td>
```

#### 3-4. Adviser/Winner/Signature 단일 tbody 통합
- 변경 전: 각각 별도 `<tbody>`, 사이에 `bl-body-td` 구분선 tbody
- 변경 후: 하나의 `<tbody>` 안에 세 행 연속 배치
- 효과: 오른쪽으로 분리 렌더링되던 버그 완전 해결

### 최종 레이아웃 수치
| 요소 | 높이 |
|------|------|
| 대회명 헤더 | 7mm |
| TEAM SCORE SHEET 행 | 8mm |
| Event/Stage 행 | 9mm |
| Team vs Team 행 | 9mm |
| 컬럼 헤더 | 8mm |
| 경기 1개 (5 sub-row × 5.5mm) | 27.5mm |
| 경기 7개 합계 | 192.5mm |
| Adviser / Winner / Signature (각 10mm) | 30mm |
| **합계** | ~284mm (A4 margin 6mm 기준 적합) |

### 파일
- `dist/templates/print_team_scoresheet.html` 수정 및 VPS 배포
