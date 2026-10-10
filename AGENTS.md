# 데드행 기록 앱

## 지침
- 정적 웹앱(PWA), 외부 의존성 없음
  - `index.html`: 앱 전체 (HTML/CSS/JS)
  - `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`, `apple-touch-icon.png`: PWA 설치/오프라인
  - 앱 파일 수정 시 `sw.js`의 `VERSION`을 올릴 것 (네트워크 우선이라 온라인이면 바로 반영되지만 캐시 정리용)
- 데이터는 브라우저 localStorage에 저장
  - `deadhang.records.v1`: `{ "YYYY-MM-DD": [{ id, ms, at, memo }] }`
  - `deadhang.running.v1`: 진행 중인 타이머 시작 시각 (새로고침해도 유지)
- 백업 파일 형식: `{ app: "deadhang", version: 1, exportedAt, records }`. 가져오기는 항상 병합(id로 중복 제거), 기존 기록 삭제 안 함
- 로컬 실행: `python -m http.server 5173` (`.claude/launch.json`의 `deadhang`)
- 휴대폰 설치/서비스워커는 HTTPS 필요 → 외부 호스팅 필요 (배포는 사용자 확인 후 진행)

## 작업 기록
- 마지막 갱신: 2026-10-10
- 완료
  - 시작/종료 타이머(0.1초), 날짜별 기록·통계, 기록 수정·삭제·직접 추가(메모), 최근 14일 목록
  - 날짜별 최고 기록 그래프 (30일/90일/전체, 호버 툴팁, 점 클릭 시 해당 날짜로 이동)
  - 백업 내보내기(JSON 다운로드) / 가져오기(검증 + 병합)
  - PWA: manifest, 아이콘, 서비스워커(네트워크 우선 + 오프라인 캐시)
- 검증 (localhost, 모바일 375px 뷰)
  - 타이머 기록, 수정, 이전 날짜 추가 확인
  - 그래프 렌더링·호버 툴팁·기간 전환 확인
  - 서비스워커 등록 및 캐시 생성 확인
  - 가져오기: 중복 제거 병합(84→85), 잘못된 파일 거부 확인
  - 내보내기: 실제 다운로드 미확인 (코드만 작성)
- 다음 작업: HTTPS 호스팅(GitHub Pages/Netlify 등) 결정 후 배포 → 휴대폰 설치 테스트
