# 2026-04-23 m4sellma 핫 스탠바이 전환 (VPS 독립 복제)

## 배경
사용자 명시 요구: "m4sellma 는 VPS 의 완전 복제본이어야 한다. MySQL 도 각자 있어야 하고. VPS 중지 시 m4sellma 가 백업 역할."

## 발견된 이전 구조 문제
- m4sellma 에 **로컬 MySQL 없음**. .env 에 `APP_SSH_TUNNEL=true` → VPS (115.68.177.35:3306) 에 터널로 접속.
- 결과: VPS 가 죽으면 m4sellma 도 DB 접근 불가 → "백업" 의미 없음.
- kuttf_db_dev 같은 VPS 쪽 복제 DB 로는 격리 가능하지만 여전히 VPS 의존.

## 전환 작업
1. `brew install mysql` → MySQL 9.6.0 (arm64 macos26), `brew services start mysql` 자동 등록.
2. MySQL root 비번 설정 + sellma0080 계정(ALL PRIVILEGES).
3. VPS → m4sellma 4 DB 전체 복제 (mysqldump → sftp → import):
   - `tt_result` (281 KB), `kuttf_db` (58 KB, players=227 team_players=118),
     `kettf_db` (282 KB, players=447), `ktta_db` (16 KB, players=0)
   - mysqldump 경고가 덤프 파일 첫 줄에 섞이는 버그 발견 → `2>/dev/null` 로 fix.
4. `src/core/config.py` 개선: 환경변수 `APP_KUTTF_DB_NAME` 등으로 리그별 DB 이름 오버라이드 가능.
5. m4sellma `.env` 전환: `APP_SSH_TUNNEL=false` + 기존 심링크 제거 후 실 파일.
6. `kuttf_db` 등 로컬 DB 에 `withdrawn` 3컬럼 idempotent 추가 (코드와 스키마 동기화).
7. 앱 재기동:
   - LaunchAgent plist 시도 → **TCC(Full Disk Access) 이슈**로 실패: `/Volumes/Data/WWW/.../venv/pyvenv.cfg` 접근 거부.
   - `screen -dmS uvi8100/uvi8101/uvi8102 bash -c '...'` 로 detached session 기동 → 성공.

## 외부 접근 경로 재정립
- ✅ `http://www.sellma.kr/` (80→ router NAT → m4sellma) 탁구 시스템 운영 페이지
- ✅ `https://www.sellma.kr:8843` (8843 → m4sellma:8101 HTTPS, 재시작 후 복구)
- ❌ `:8100`, `:8102` 는 라우터 NAT 매핑 없음 → 외부 비공개 (필요 시 UniFi 에서 추가)
- ✅ 내부 LAN: `http://192.168.1.202:8100/8102` 직접 접근

## 실데이터 smoke 결과
- `GET /health` 200
- `GET /my` 200 (R2-01 세로 랜딩)
- `GET /api/v1/players/search?q=홍` 200 → 실선수 (홍기주, 김동환/홍기주 복식) 반환
- `GET /api/v1/my-matches` 200

## 남은 과제
- `kettf_db.team_players` withdrawn 컬럼 (초등 전용, 다음 사이클)
- LaunchAgent 정식 등록 (Mac mini GUI 에서 Full Disk Access 부여 후)
- 정기 sync cron 등록 (`scripts/vps_to_m4_sync.py`)

## 신규 자산
- 스크립트 11종: `m4sellma_*.py` (rehearsal, investigate, mysql setup, switch_to_local, apply_withdrawn 등), `vps_to_m4_sync.py`, `vps_create_dev_db.py`.
- 문서: `docs/release/2026-04-23-hot-standby-setup.md`, `docs/release/2026-04-23-m4sellma-rehearsal-report.md`.
