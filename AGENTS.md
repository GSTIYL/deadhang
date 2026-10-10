# 운동기록 앱 (데드행)

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
- 배포: GitHub Pages (`main` 브랜치 루트) — 저장소 https://github.com/GSTIYL/deadhang (public), 주소 https://gstiyl.github.io/deadhang/
  - `main`에 push하면 자동 재배포. push는 사용자 요청 시에만
  - gh CLI 경로: `C:\Program Files\GitHub CLI\gh.exe` (PowerShell에서는 `& "경로"`로 실행)

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
  - 배포: GitHub Pages 빌드 완료, 페이지·manifest·아이콘 HTTP 200 확인 (브라우저 패널 접속은 차단돼 실기기 미확인)
  - 타이머 보강: 카운트다운(끔/3/5초), 목표 시간·중간 알림(소리·Android 진동), 화면 꺼짐 방지(Wake Lock), 무게·그립 기록, 달력·연속일
    - 확인: 알림 타이밍(가짜 시계로 카운트다운→시작→15초→목표 20초→30초), 무게·그립 저장/표시, 달력 표시·연속일·날짜 클릭
    - 미확인: 화면 꺼짐 방지·진동·소리는 실제 휴대폰에서만 확인 가능
  - 설정 저장 키: `deadhang.settings.v1` (countdown, goalSec, interval, sound, weight, grip)
- 다음 작업: 휴대폰에서 화면 꺼짐 방지·소리·진동 확인
