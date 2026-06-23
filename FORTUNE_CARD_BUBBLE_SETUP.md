# Feature 05: 오늘의 카드 — Bubble 설정 가이드 (최종)

**배포 URL**
- 요약 카드: `https://weather-app-pied-theta.vercel.app/fortune-card.html`
- 상세 페이지: `https://weather-app-pied-theta.vercel.app/fortune.html`
- API: `https://weather-app-pied-theta.vercel.app/api/fortune` (POST, iframe 직접 호출)

---

## 아키텍처 원칙

날씨 기능과 동일한 패턴:
- **Bubble → iframe**: URL 파라미터로 데이터 주입
- **iframe → Bubble**: postMessage 단방향 신호 (응답 안 기다림)
- **카드 생성**: iframe이 `/api/fortune` 직접 호출 (Bubble 관여 없음)
- **티켓 차감**: iframe이 카드 열람 후 Bubble에 신호만 보냄

---

## 확인된 User 필드

| 필드명 | 타입 | 용도 |
|---|---|---|
| `tickets` | number (default: 0) | 잔여 티켓 수 |
| `birth_date` | date | 생년월일 (있으면 URL로 전달) |

---

## 1단계 — 홈 요약 카드 iframe (fortune-card.html)

### iframe URL 수식
```
"https://weather-app-pied-theta.vercel.app/fortune-card.html"
& "?tickets=" & Current User's tickets
```

- `cached_main` 파라미터: 일단 생략 (구현 단순화)
- 높이: 170px, 배경: transparent

### 탭 이벤트 처리
iframe에 **JavascripttoBubble** 요소 추가:
- Property name: `fortune_card_msg` (text)

홈 페이지 `Page is loaded` 워크플로우에 JS 추가:
```javascript
window.addEventListener('message', function(e) {
  if (e.data && e.data.type === 'fortune_card_tapped') {
    bubble_fn_fortune_card_msg('tapped');
  }
});
```

`fortune_card_msg` 값이 `tapped`로 바뀌면 → 상세 페이지 그룹 Show

---

## 2단계 — 상세 페이지 iframe (fortune.html)

### 위치
홈 메인 페이지 내 별도 그룹 (SPA 방식) — 요약 카드 탭 시 Show, 뒤로가기 시 Hide

### iframe URL 수식
```
"https://weather-app-pied-theta.vercel.app/fortune.html"
& "?tickets=" & Current User's tickets
& "&name=" & Current User's name
& "&birth_date=" & Current User's birth_date:formatted as "YYYY-MM-DD"
& "&gender=unknown"
```

> `birth_date`가 비어있으면 빈 문자열이 됨 → iframe이 알아서 "정보 입력" 바텀시트 안내

---

## 3단계 — 워크플로우 A: 티켓 차감 (fortune_ticket_used)

iframe이 서브 카드(러닝운/인연운/사주풀이) 열람 후 보내는 신호

### JavascripttoBubble 설정
- Property name: `fortune_msg` (text)

상세 페이지 그룹이 **visible** 될 때 JS 실행:
```javascript
window.addEventListener('message', function(e) {
  if (!e.data || !e.data.type) return;
  if (e.data.type === 'fortune_ticket_used') {
    bubble_fn_fortune_msg('ticket_used');
  }
  if (e.data.type === 'fortune_save_birth') {
    bubble_fn_fortune_msg('save_birth|' + (e.data.birth_date || ''));
  }
  if (e.data.type === 'fortune_go_to_pacer') {
    bubble_fn_fortune_msg('go_to_pacer');
  }
});
```

### Workflow: fortune_msg가 "ticket_used"로 변경될 때
1. **Condition**: `fortune_msg` = `ticket_used`
2. **Make changes to Current User**:
   - `tickets` = `Current User's tickets - 1`
3. `fortune_msg` 값 초기화 (빈 문자열로)

---

## 4단계 — 워크플로우 B: 생년월일 저장 (fortune_save_birth)

iframe이 최초 메인 카드 생성 시 보내는 신호

### Workflow: fortune_msg가 "save_birth|"로 시작할 때
1. **Condition**: `fortune_msg` contains `save_birth|`
2. **Make changes to Current User**:
   - `birth_date` = `fortune_msg`에서 `|` 뒤 날짜 파싱
   
> 팁: Bubble에서 `fortune_msg:find and replace(text to find: "save_birth|", replacement: "")` 로 날짜만 추출 가능

---

## 5단계 — 워크플로우 C: 인연운 CTA (go_to_pacer)

1. **Condition**: `fortune_msg` = `go_to_pacer`
2. **Action**: Navigate to Daily Pacer 탭 (또는 해당 그룹 Show)

---

## postMessage 인터페이스 요약

### iframe → Bubble (단방향, 응답 없음)
| type | 추가 데이터 | 타이밍 |
|---|---|---|
| `fortune_card_loaded` | - | iframe 로드 완료 |
| `fortune_card_tapped` | - | 요약 카드 탭 |
| `fortune_save_birth` | `birth_date: "YYYY-MM-DD"` | 최초 메인 카드 생성 시 |
| `fortune_ticket_used` | `fortune_type: "running"` 등 | 서브 카드 열람 후 |
| `fortune_go_to_pacer` | - | 인연운 CTA 탭 |

### Bubble → iframe
없음. iframe이 독립적으로 동작함.

---

## 티켓 지급 로직

### 신규 가입 시
- `tickets` default = 0
- 가입 완료 워크플로우에서 `tickets = 3` 설정 (또는 현재 기본값 유지)

### 일일 무료 티켓 (선택)
- Backend workflow 매일 자정 실행
- `tickets` < 3 이면 `tickets = tickets + 1`

---

## 테스트 URL

```
https://weather-app-pied-theta.vercel.app/fortune.html?tickets=5&name=테스트&birth_date=1990-05-15
```

- birth_date 없는 경우 (0단계): `?tickets=3&name=테스트`
- 모든 카드 바로 열람: `?tickets=5&name=홍길동&birth_date=1990-05-15`
