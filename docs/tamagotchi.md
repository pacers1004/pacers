# 페이서 다마고치 — 상세 문서

> **상태**: 3탭 UI + 랭킹 + 마이탭 + 랭킹→유저카드 연결까지 완성. **블로커: 스크롤+오버레이 구조 충돌** (아래 참고). 다음 세션 첫 작업 = 오버레이 분리.
> **7월 목표** — 매일 앱 열 이유 → DAU 개선 핵심

---

## 파일 (weather-app, 모두 라이브 배포됨)

| 파일 | 역할 |
|------|------|
| `cat.html` | 홈 요약카드 (높이 170px) |
| `cat_detail.html` | 냥이 상세 (3탭: 내냥이/랭킹/마이) |
| `cat_input.html` | 입력 전용 폼 — 버블 Popup 안에 삽입 |
| `cat_evo.html` | 🔴 미생성 — 다음 세션 첫 작업 |
| `api/ranking.js` | 랭킹 API (lifetime/today/week/mystats) |

---

## 캐릭터 시스템

### 레벨 (누적 km 기준)
Lv.0 알(0) / Lv.1 아기냥(10) / Lv.2 새벽냥(30) / Lv.3 뚱냥이(80) / Lv.4 페이서냥(200) / Lv.5 엘리트냥(500) / Lv.6 마라토너냥(1000)

### 상태 (last_run_date 기준 — 클라에서 계산)
- happy: 오늘 달림
- normal: 1일 쉼
- sad: 2일 쉼
- danger: 3일+ 쉼
- egg: 7일+ 쉼 → 레벨-1 퇴화

---

## 버블 배선 (완료)

### User 신규 필드 4개
- `tamagotchi_lifetime_km` (number)
- `tamagotchi_streak` (number)
- `tamagotchi_last_run_date` (date)
- `tamagotchi_longest_streak` (number)

### JavascripttoBubble 엘리먼트
- `JavascripttoBubble 냥이` — Multiple Outputs, output1=number(거리), output2=text(감정), output3=number(분), output4=text(메모), Publish value ✓

### 다리 스크립트 (페이지 HTML 엘리먼트)
```js
window.addEventListener('message', function(e) {
  if (e.data && e.data.action === 'rlog_values')
    bubble_fn_rlog({output1:e.data.km, output2:e.data.emotion, output3:e.data.minutes, output4:e.data.memo});
  if (e.data && e.data.action === 'open_user' && e.data.id)
    bubble_fn_openUser({output1: e.data.id});
  // open_user → JavascripttoBubble openUser → Go to page 01_main, user_id=output1, tab=daily_card
});
```

### 워크플로우: "러닝 기록 완료하기" 클릭
- Step1 Create DailyRunningLog: distance_km=output1, emotion=EmotionType(output2), duration_sec=output3×60, comment=output4
- Step2 Make changes User: tamagotchi_lifetime_km += output1, tamagotchi_last_run_date=now
- Step3~7: 기존 퀘스트/티켓 로직 유지
- 끝 Step: Run JS → `document.getElementById('cat-detail').contentWindow.postMessage({action:'didRun',km:output1,new_lifetime:타마고치_lifetime_km},'*')`

### EmotionType 옵션셋
Display = happy/proud/tired (소문자, iframe 값과 일치)

### iframe 리로드 방지 (커스텀 상태 스냅샷)
`01_다마고치 냥이_tab`에 상태 5개: `s_life, s_today, s_week, s_streak, s_longest` (모두 number)
- 진입 시 1회만 스냅샷 → src 바인딩 → 기록해도 리로드 안 함 → didRun 연출 살아남

### iframe URL 파라미터 (cat_detail)
```
?streak=[s_streak]&longest=[s_longest]&lifetime_km=[s_life]&today_km=[s_today]&week_km=[s_week]&onb=0&embed=1
```

---

## 🔴🔴 최우선 블로커 — 다음 세션 첫 작업

**증상**: 진화 팝업 "확인" 버튼이 해상도에 따라 안 눌림 + 세로 스크롤 먹통

**근본 원인**: `position:fixed` 오버레이 + 긴 스크롤 컨텐츠를 한 iframe에서 동시에 — 양립 불가.

**확정 해결책: 오버레이를 iframe 밖으로 빼기**

1. `cat_detail.html` = 2100px 페이지스크롤 (in-iframe 오버레이 코드 전부 비활성)
2. `cat_evo.html` 신규 생성 (진화 비주얼 전용 작은 iframe)
3. `didRun` → 레벨업 시 → `show_evo` postMessage → 버블 다리 → "진화 Popup" Show
4. "확인" = 버블 네이티브 버튼 → 어떤 해상도든 무조건 닫힘 ✅

**작업 순서:**
1. cat_detail → 2100px 페이지스크롤 고정 (버블 박스 3개 fit-content)
2. cat_evo.html 신규 생성 (진화 비주얼 전용)
3. cat_detail didRun → 레벨업 시 show_evo postMessage (in-iframe 오버레이 제거)
4. 버블: 진화 Popup + 다리에 show_evo 수신 + 작은 cat_evo iframe
5. 졸업도 동일 패턴

**왜 입력 팝업과 동일 검증된 패턴인가**: cat_input.html을 버블 Popup 안에 넣어서 이미 잘 작동 중. 진화도 같은 방식.

---

## api/ranking.js

- `type=lifetime`: User.tamagotchi_lifetime_km 정렬
- `type=today/week`: DailyRunningLog 날짜 필터 → 유저별 합산 → 닉네임/사진 조인
- `type=mystats`: 내 전체 로그 집계 (totalRuns, longestRun, totalKm, bestPaceSec)
- `Cache-Control: no-store`
- `BUBBLE_BASE`: 현재 `version-test` → **라이브 배포 직전 `api/1.1/obj`로 교체 필요**

---

## QA / 개선 도출 (§33-C 요약)

### 🔴 보안 — 가장 심각
- 기록(거리)이 클라 입력 → JTB → 워크플로우로 그대로 저장 + 퀘스트 티켓 지급됨
- 1일 1회 가드 미구현 → 무제한 기록 → 티켓 파밍 가능
- **개선**: 워크플로우 조건 `Search DailyRunningLogs(user=Current User, date>=오늘):count is 0` 일 때만 티켓 지급 + 거리 상한 >100km 거부

### 🟡 버블 워크로드
- today_km/week_km = Search:sum 이 홈 로드마다 실행됨 → 부담 큼
- **개선**: User에 `tamagotchi_today_km`/`tamagotchi_week_km` 비정규화 필드 추가 → Search 0개

### 🟠 status/last_run_date 미연결
- src에 `status`/`last_run_date` 안 넘김 → 냥이 영원히 normal
- **개선**: src에 `last_run_date=[epoch ms]` 1개 추가 → iframe이 클라에서 전부 계산

### 우선순위
1. 블로커 분리 (스크롤+오버레이)
2. status 연결 (last_run_date 1개 추가)
3. 보안 가드 (티켓 파밍 차단)
4. 비정규화 (Search 제거)

---

## 홈 요약카드 (cat.html) 디자인

- 좌측: 누적 km hero (Anton 폰트 skewX -7deg) + LV 블루 pill 뱃지 + 파란 XP바
- 우측: 냥이 SVG 캐릭터 (76px 고정 컬럼)
- 하단: `오늘 X · 이번주 Y · 🔥Z일` 미니 스탯 한 줄
- 높이 170px

## 진화 연출 postMessage 구조 (구현 예정)
```js
// cat_detail → 버블 다리
window.parent.postMessage({action:'show_evo', from:2, to:3, charName:'뚱냥이', line:'달리기가 습관이 됐어요!'}, '*');

// 버블 다리 → 진화 Popup Show
// cat_evo.html src = ?from=2&to=3&name=뚱냥이&line=...
```
