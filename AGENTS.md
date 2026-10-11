# 운동기록 앱 (데드행)

## 지침
- 정적 웹앱(PWA), 빌드 도구·외부 의존성 없음
  - `index.html`: 앱 전체 (HTML/CSS/JS 한 파일)
  - `manifest.webmanifest`, `sw.js`, `icon-192.png`, `icon-512.png`, `apple-touch-icon.png`: PWA 설치/오프라인
  - 앱 파일 수정 시 `sw.js`의 `VERSION`을 올릴 것 (현재 `deadhang-v4`). 네트워크 우선 캐시라 온라인이면 바로 반영됨
- 데이터는 브라우저 localStorage에만 저장 (기기 간 동기화 없음). 키 이름의 `deadhang`은 기존 기록 호환을 위해 유지
  - `deadhang.records.v1`: `{ "YYYY-MM-DD": [{ id, ms, at, memo, weight, grip }] }` — weight(kg, 숫자, 0=없음), grip(문자열, ''=없음)은 이전 기록에 없을 수 있음
  - `deadhang.running.v1`: 진행 중 타이머 시작 시각. 카운트다운 중엔 미래 시각(음수 경과 = 카운트다운)
  - `deadhang.settings.v1`: `{ countdown, goalSec, interval, sound, weight, grip }`
- 백업 파일 형식: `{ app: "deadhang", version: 1, exportedAt, records }`. 가져오기는 항상 병합(id로 중복 제거), 기존 기록 삭제 안 함
- 로컬 실행: `python -m http.server 5173` (`.claude/launch.json`의 `deadhang`, `.claude/`는 gitignore)
- 배포: GitHub Pages (`main` 브랜치 루트)
  - 저장소 https://github.com/GSTIYL/deadhang (public), 주소 https://gstiyl.github.io/deadhang/
  - `main`에 push하면 1~2분 내 자동 재배포. push는 사용자 요청 시에만
  - gh CLI: `C:\Program Files\GitHub CLI\gh.exe` (PowerShell에서는 `& "경로"`로 실행), GSTIYL 계정 로그인됨
- 테스트 팁: 브라우저 패널 탭이 화면에 안 보이면 requestAnimationFrame이 멈춰 타이머가 안 움직임 → 타이머 로직은 `Date.now`를 가짜로 바꾸고 `tick()`을 직접 호출해 검증. 테스트 후 `localStorage.clear()`로 정리

## 작업 기록 (인수인계)
- 마지막 갱신: 2026-10-12
- 상태: 모든 변경 커밋·배포 완료, 로컬과 origin/main 동기화됨
- 구현된 기능
  - 타이머: 시작/종료(0.1초), 카운트다운(끔/3/5초, 중 '취소'), 목표 시간 진행 막대·달성 알림, 중간 알림(15/30/60초), 소리(WebAudio)·진동(Android), 화면 꺼짐 방지(Wake Lock), 새로고침 후 이어가기
  - 기록: 날짜별 목록·통계(세트/최고/합계), 수정·삭제·직접 추가, 무게·그립·메모
  - 보기: 날짜별 최고 기록 그래프(30/90일/전체, 툴팁, 점 클릭 이동), 달력(운동일 표시·연속일·월 운동일, 날짜 클릭 이동), 최근 14일 목록
  - 백업 내보내기/가져오기, PWA 설치·오프라인
- 검증 완료 (localhost, 375px 모바일 뷰)
  - 타이머 기록·수정·추가, 그래프, 서비스워커/캐시, 가져오기 병합(중복 제거)·잘못된 파일 거부
  - 알림 타이밍(가짜 시계: 카운트다운→시작음→15초→목표 20초→30초), 무게·그립 저장/표시, 달력·연속일·날짜 클릭
  - 배포 사이트에 최신 버전 반영 확인 (HTTP로 확인, 브라우저 패널은 github.io 접속 차단됨)
- 미확인
  - 실제 휴대폰: 홈 화면 설치, 오프라인, 화면 꺼짐 방지, 소리, 진동
  - 내보내기 버튼의 실제 파일 다운로드
- 다음 작업 후보
  - 휴대폰 실기기 확인 결과에 따른 수정
  - 이전에 제안했으나 미착수: 신기록 알림, 기록 보호(storage.persist + 백업 알림), 운동 종류 추가(플랭크 등), 휴식 타이머, 종료 깜빡함 대비(장시간 경고)
