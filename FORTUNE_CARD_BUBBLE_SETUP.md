# Feature 05: 오늘의 카드 — Bubble 설정 가이드

**배포 URL**
- 요약 카드: `https://weather-app-pied-theta.vercel.app/fortune-card.html`
- 상세 페이지: `https://weather-app-pied-theta.vercel.app/fortune.html`
- API: `https://weather-app-pied-theta.vercel.app/api/fortune` (POST)

---

## 1단계 — Bubble User 테이블 필드 추가

| 필드명 | 타입 | 설명 |
|---|---|---|
| `birth_date` | text | 생년월일 YYYY-MM-DD |
| `birth_time` | text | 출생시간 HH:MM (없으면 빈 문자열) |
| `birth_time_unknown` | yes/no | 시간 모름 여부 |
| `lunar_calendar` | yes/no | 음력 여부 |
| `fortune_tickets` | number | 보유 티켓 수 (기본값 3) |

---

## 2단계 — DailyFortune 데이터 타입 생성

새 데이터 타입 `DailyFortune` 생성:

| 필드명 | 타입 | 설명 |
|---|---|---|
| `fortune_user` | User | 소유 유저 |
| `fortune_date` | date | 운세 날짜 (date only) |
| `fortune_type` | text | main / running / love / saju |
| `card_name` | text | 카드명 |
| `content` | text | 운세 내용 |
| `saju_raw` | text | 사주 JSON 문자열 (API 응답 그대로) |

---

## 3단계 — 홈 화면 요약 카드 (fortune-card.html) iframe 설정

### 위치
01_main 홈 화면 — Daily Pacer 카드 아래 (또는 날씨 카드 아래)

### iframe 설정
- **URL 수식**:
```
"https://weather-app-pied-theta.vercel.app/fortune-card.html"
& "?tickets=" & Current User's fortune_tickets
& "&cached_main=" & (URL-encoded JSON of today's main fortune, if exists)
```

### `cached_main` 만드는 법
페이지 로드 시 `Do a search for DailyFortune` 로 오늘 날짜 + Current User + type=main 검색:
```
Do search: DailyFortune
  fortune_user = Current User
  fortune_date = Today's date (no time)
  fortune_type = "main"
  :first item
```

있으면 → `{"card_name":"...","content":"..."}` 형태로 조합 후 URL encode

### JavascripttoBubble 설정 (fortune-card iframe 이벤트)
iframe이 발송하는 이벤트를 받기 위해 페이지에 **JavascripttoBubble** 요소 추가:
- Element: `JavascripttoBubble`
- Property: `fortune_message` (type: text)
- 홈 페이지 `Page is loaded` 워크플로우에서 아래 JS 실행:
```javascript
window.addEventListener('message', function(e) {
  if (!e.data || !e.data.type) return;
  if (e.data.type === 'fortune_card_tapped') {
    bubble_fn_fortune_message('fortune_card_tapped');
  }
  if (e.data.type === 'fortune_card_loaded') {
    bubble_fn_fortune_message('fortune_card_loaded');
  }
});
```
- `fortune_message` 값이 `fortune_card_tapped`로 변경되면 → 상세 페이지로 라우팅

---

## 4단계 — 상세 페이지 (fortune.html) iframe 설정

### 위치
별도 팝업 그룹 또는 페이지 전환으로 표시

### URL 수식 (전체)
```
"https://weather-app-pied-theta.vercel.app/fortune.html"
& "?user_id=" & Current User's unique id
& "&birth_date=" & Current User's birth_date
& "&birth_time=" & Current User's birth_time
& "&birth_time_unknown=" & (Current User's birth_time_unknown = "yes" : "1" : "0")
& "&lunar_calendar=" & (Current User's lunar_calendar = "yes" : "1" : "0")
& "&gender=" & Current User's gender (male/female)
& "&tickets=" & Current User's fortune_tickets
& "&cached_main=" & (today's main fortune JSON, URL-encoded or "")
& "&cached_running=" & (today's running fortune JSON, URL-encoded or "")
& "&cached_love=" & (today's love fortune JSON, URL-encoded or "")
& "&cached_saju=" & (today's saju fortune JSON, URL-encoded or "")
```

### JavascripttoBubble 설정 (fortune 상세 iframe)
페이지에 **JavascripttoBubble** 요소 추가:
- Property: `fortune_detail_message` (type: text)
- 페이지/팝업 `is visible` 이벤트에서 JS 실행:
```javascript
window.addEventListener('message', function(e) {
  if (!e.data || !e.data.type) return;
  var msg = e.data;
  switch(msg.type) {
    case 'fortune_save_birth_info':
      bubble_fn_fortune_detail_message(JSON.stringify(msg));
      break;
    case 'fortune_request':
      bubble_fn_fortune_detail_message(JSON.stringify(msg));
      break;
    case 'fortune_card_loaded':
      bubble_fn_fortune_detail_message('fortune_card_loaded');
      break;
    case 'fortune_go_to_pacer':
      bubble_fn_fortune_detail_message('fortune_go_to_pacer');
      break;
  }
});
```

---

## 5단계 — 워크플로우: 사주 정보 저장 (fortune_save_birth_info)

**트리거**: `fortune_detail_message` 값이 변경되고 `contains "fortune_save_birth_info"`

**Steps:**
1. **Only when**: `fortune_detail_message` contains `"fortune_save_birth_info"`
2. **Run JavaScript** → JSON 파싱하여 Bubble state에 저장:
```javascript
var msg = JSON.parse(bubble_fn_fortune_detail_message());
// 아래는 Bubble 워크플로우에서 직접 처리
```
3. **Make changes to Current User**:
   - `birth_date` = JSON에서 `birth_date` 값
   - `birth_time` = JSON에서 `birth_time` 값
   - `birth_time_unknown` = JSON에서 `birth_time_unknown` 값
   - `lunar_calendar` = JSON에서 `lunar_calendar` 값
4. **Run JavaScript** (iframe에 저장 완료 응답):
```javascript
document.getElementById('fortune-iframe').contentWindow.postMessage(
  { type: 'fortune_birth_info_saved' }, '*'
);
```
   → iframe ID는 실제 설정한 Element ID로 교체

---

## 6단계 — 워크플로우: 운세 요청 처리 (fortune_request)

**트리거**: `fortune_detail_message` 값이 변경되고 `contains "fortune_request"`

**Steps:**

### Step 1 — 티켓 확인 (main 제외)
- **Condition**: fortune_type ≠ "main" AND Current User's fortune_tickets ≤ 0
- **If true**: Run JavaScript → iframe에 에러 응답:
```javascript
document.getElementById('fortune-iframe').contentWindow.postMessage(
  { type: 'fortune_error', message: '티켓이 부족해요' }, '*'
);
```
- **Stop here**

### Step 2 — 티켓 차감 (main 제외)
- **Only when**: fortune_type ≠ "main"
- **Make changes to Current User**: `fortune_tickets` = `fortune_tickets - 1`

### Step 3 — 오늘 이미 열람한 기록 확인
- **Do search**: DailyFortune where `fortune_user = Current User`, `fortune_date = Today`, `fortune_type = [요청 type]`
- **If found**: Step 5로 바로 응답 (재계산 없이 캐시 반환)

### Step 4 — Vercel API 호출 (새로운 운세)
- **API Connector** 또는 **Backend Workflow** → POST `https://weather-app-pied-theta.vercel.app/api/fortune`
- **Headers**: `Content-Type: application/json`
- **Body**:
```json
{
  "birth_date":          "[Current User's birth_date]",
  "birth_time":          "[Current User's birth_time]",
  "birth_time_unknown":  "[Current User's birth_time_unknown]",
  "lunar_calendar":      "[Current User's lunar_calendar]",
  "gender":              "[Current User's gender]",
  "fortune_type":        "[요청한 type]",
  "today_date":          "[오늘 날짜 YYYY-MM-DD 포맷]"
}
```

### Step 5 — DailyFortune DB 저장
- **Create a new DailyFortune**:
  - `fortune_user` = Current User
  - `fortune_date` = Today (date only)
  - `fortune_type` = 요청한 type
  - `card_name` = API 응답의 `card_name`
  - `content` = API 응답의 `content`
  - `saju_raw` = API 응답의 `saju_raw` (JSON.stringify)

### Step 6 — iframe에 결과 응답
```javascript
document.getElementById('fortune-iframe').contentWindow.postMessage({
  type:         'fortune_result',
  fortune_type: '[요청 type]',
  card_name:    '[API 응답 card_name]',
  content:      '[API 응답 content]',
  saju_raw:     null  // 또는 JSON 파싱된 객체
}, '*');
```

---

## 7단계 — 워크플로우: 인연운 CTA (fortune_go_to_pacer)

**트리거**: `fortune_detail_message` = `"fortune_go_to_pacer"`

**Action**: Go to Daily Pacer 탭 (Navigate 또는 Show Group)

---

## 8단계 — 티켓 지급 로직

### 신규 유저 가입 시
- `fortune_tickets` = 3 (기본 지급)

### 일일 무료 티켓 (선택)
- **Backend Workflow**: 매일 00:00 KST에 실행
- 모든 유저에게 `fortune_tickets` += 1 (최대 3 cap)

### RevenueCat 결제 후 티켓 추가
- RevenueCat webhook → Bubble Backend Workflow
- `fortune_tickets` += 구매한 티켓 수

---

## postMessage 인터페이스 요약

### iframe → Bubble (발송)
| type | 추가 필드 | 설명 |
|---|---|---|
| `fortune_card_loaded` | - | iframe 로드 완료 |
| `fortune_card_tapped` | - | 요약 카드 탭 (홈 → 상세 라우팅) |
| `fortune_save_birth_info` | birth_date, birth_time, birth_time_unknown, lunar_calendar | 사주 정보 저장 요청 |
| `fortune_request` | fortune_type | 운세 열람 요청 (티켓 차감은 버블에서) |
| `fortune_go_to_pacer` | - | 인연운 CTA → Daily Pacer 탭 이동 |

### Bubble → iframe (발송)
| type | 추가 필드 | 설명 |
|---|---|---|
| `fortune_birth_info_saved` | - | 사주 정보 저장 완료 → 잠금 해제 |
| `fortune_result` | fortune_type, card_name, content, saju_raw | 운세 결과 → 카드 플립 |
| `fortune_error` | message | 에러 (티켓 부족 등) |

---

## API 응답 예시

**성공:**
```json
{
  "card_name": "절기의 전환점",
  "content": "오늘 에너지가 안으로 모이는 날이야. 빠르게 달리기보다 천천히 자기 페이스를 찾아봐. 리듬이 잡히면 나머지가 따라와.",
  "saju_raw": { "year":"경오", "month":"신사", "day":"경진", "hour":"계미" }
}
```

**폴백 (Claude 에러 시 — 항상 200):**
```json
{
  "card_name": "고요한 출발선",
  "content": "억지로 속도 내지 않아도 돼. 오늘은 워밍업하듯 천천히 시작하는 게 딱 맞아.",
  "saju_raw": null,
  "_fb": true
}
```

---

## 주의사항

- **iframe이 직접 API 호출하거나 티켓 차감 금지** — 반드시 버블 워크플로우 경유
- `fortune_tickets`는 서버사이드(Bubble)에서만 증감
- 같은 유저 + 같은 날짜 + 같은 type은 DB 캐시 재활용 (API 중복 호출 방지)
- `saju_raw`는 텍스트(JSON string)로 저장, 필요 시 파싱하여 사용
