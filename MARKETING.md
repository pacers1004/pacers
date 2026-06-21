# 페이서스 마케팅 플랜 (MARKETING.md)

> **목표**: 국내 1위 러닝 플랫폼 — 회원 2,000명 돌파 (현재 1,079명, 2026-06-21 기준)
> **전략**: 콘텐츠 기반 유기적 성장 + 커뮤니티 바이럴 + 리텐션 루프

---

## 1. 마케팅 철학

> "멀리, 빨리 뛰는 법보다 오늘도 운동화를 다시 신는 법을 제안합니다."

페이서스 마케팅은 **러너의 감정에 공감하는 콘텐츠**가 핵심이다.
스펙/기능 홍보보다 "이 앱 쓰는 사람들의 이야기"로 신뢰를 쌓는다.

### 타겟
- **1순위**: 20~35세 도시 러너, 혼자 달리지만 연결되고 싶은 사람
- **2순위**: 마라톤 입문자, 러닝 크루 찾는 사람
- **3순위**: 건강 관심 있지만 달리기 습관 아직 없는 사람

---

## 2. 채널 전략

### A. Instagram (핵심 채널)
- **포맷**: 카드뉴스 5장 캐러셀 (Story Arc 구조)
- **자동화**: Make.com 매일 18:30 KST 자동 발행 (§4 참고)
- **콘텐츠**: 러너 공감 → 반전 인사이트 → 감각 묘사 → 성취 → 위인 명언
- **톤**: 블라인드 러닝 채널 감성 — 솔직하고 담담하게, 자기계발 X

### B. 앱 내 콘텐츠 (리텐션)
- **쿨다운**: 매일 9회 자동 게시 (cron-job.org → Claude API)
- **데일리 퀘스트**: 러닝기록 + 쿨다운 → 무료 티켓 → DAU 유지
- **첫인상 보내기**: 저마찰 매칭 워밍업 → 전환율 ↑

### C. 친구 초대 (바이럴)
- 초대 시 30티켓 보상 → 최고 단가 인센티브
- 딥링크: ChottuLink (`pacers.chottu.link`)

---

## 3. 콘텐츠 전략

### Story Arc V2 — 인스타 캐러셀 5장 구조

```
Card 1: 공감 (Hook) — "나도 이런 생각 했다"
Card 2: 이유 (Flip) — 반전 인사이트
Card 3: 감각 (Immersion) — 달릴 때의 구체적 감각
Card 4: 성취 (Achievement) — 오늘 뛴 것의 의미
Card 5: 위인 명언 (Quote) — 맥락에 맞는 인용
```

**텍스트 규칙**:
- 한 카드당 2~3줄, 줄당 ~10자
- `\n`으로 줄 구분
- 금지: 날씨/계절/날짜/지명/대회명/자기계발어/격식체 종결어미
- 금지: 새벽런/야간런/해장달리기

### 100세트 프리셋 (100일치 유니크 콘텐츠)

Make.com Data Store에 100세트 저장 → 매일 순차 발행 (100일 후 반복 없음)

**위인 목록 (Sets 1~100)**:
무하마드 알리, 마이클 조던, 코베 브라이언트, 오프라 윈프리, 스티브 잡스,
넬슨 만델라, 윈스턴 처칠, 마틴 루터 킹, 알베르트 아인슈타인, 헬렌 켈러,
토마스 에디슨, 헨리 데이비드 소로우, 마하트마 간디, 빌 게이츠,
마르쿠스 아우렐리우스, 엘론 머스크, 소크라테스, 시어도어 루스벨트,
마더 테레사, 브루스 리, 데이비드 고긴스, 에밀 자토펙, 요기 베라,
마야 안젤루, 빅터 프랭클, 세네카, 아리스토텔레스, 프리드리히 니체,
에이브러햄 링컨, 워렌 버핏, 에픽테토스, 루미, 공자, 짐 론,
랠프 왈도 에머슨, 칼 루이스, 헨리 포드, 월트 디즈니, 파블로 피카소,
레오나르도 다빈치, 찰스 다윈, 플라톤, 달라이 라마, 마크 트웨인,
오스카 와일드, 나폴레옹 보나파르트, 손자, 노자, 니콜라 테슬라,
마리 퀴리, 벤저민 프랭클린, 레프 톨스토이, 헤르만 헤세, 알베르 카뮈,
파울로 코엘료, 어니스트 헤밍웨이, 리처드 파인만, 칼 세이건,
조지 버나드 쇼, 버트런드 러셀, 코코 샤넬, 아멜리아 에어하트,
엘리너 루스벨트, 재키 로빈슨, 조 디마지오, 피에르 드 쿠베르탱,
헤라클레이토스, 킵초게 엘리우드, 하일레 게브르셀라시에, 아베베 비킬라,
정주영, 이순신, 손흥민, 김연아, 앙투안 드 생텍쥐페리, 로저 배니스터,
바라크 오바마, 마가렛 대처, 칼 융, 미켈란젤로, 베토벤, 토니 모리슨,
앤 설리번, 임마누엘 칸트, 스피노자, 볼테르, 장 자크 루소, 빅토르 위고,
표도르 도스토예프스키, 잭 마, 사티야 나델라, 제프 베조스, 리처드 브랜슨,
레이 달리오, 알렉산더 대왕, 폴라 래드클리프, 세바스티안 코, 빌리 밀스,
박지성, 황영조

**파일**: `/Users/janghara/Desktop/페이서스 출시 준비/running_carousel_100sets_complete.json`

---

## 4. 인스타 카드뉴스 자동화 (Make.com)

### 시스템 구조

```
[Make.com Scenario 6263718]
"Pacers 일일 캐러셀 5장 18:30 KST"
  │
  ├─ 매일 18:30 KST 자동 실행
  │
  ├─ Module: Vercel /api/template 호출
  │    → KST 날짜 % 3 → 템플릿 UUID 반환
  │    → 블랙/블루/퍼플 3일 사이클 로테이션
  │
  ├─ Module: Make.com Data Store에서 당일 세트 조회
  │    → set_id 순차 (1→2→3...→100→1)
  │    → card_1~5 텍스트 + card_5 위인 이름
  │
  ├─ Module: Placid API (이미지 생성)
  │    → 카드 1~5 각각 POST https://api.placid.app/api/rest/images
  │    → layer: "New text layer" (3개 템플릿 공통)
  │    → create_now: false (async 모드)
  │    → Sleep 25초 (Vercel /api/sleep?ms=25000)
  │    → 이미지 URL 수집
  │
  └─ Module: Instagram API
       → 5장 캐러셀 업로드 (18:30 KST)
```

### Placid 템플릿 3종

| 이름 | UUID | 사용일 (KST day % 3) |
|------|------|---------------------|
| 블랙 (Black) | `l4ugi3lmvr3en` | 0일 (월/목 기준) |
| 블루 (Blue/Quote) | `zxprljfehgozj` | 1일 |
| 퍼플 (Purple/Testimonial) | `lxt4xvqnjeaz6` | 2일 |

**공통 레이어명**: `"New text layer"` (3개 템플릿 모두 동일)

### Vercel 지원 함수 (weather-app 프로젝트)

| 함수 | URL | 역할 |
|------|-----|------|
| `/api/template` | `weather-app-pied-theta.vercel.app/api/template` | KST 날짜 기반 템플릿 UUID 반환 |
| `/api/sleep` | `weather-app-pied-theta.vercel.app/api/sleep?ms=25000` | 이미지 생성 대기 (httpbin 대체) |
| `/api/cooldown` | `weather-app-pied-theta.vercel.app/api/cooldown` | 쿨다운 자동게시 |

**Template API 코드** (`weather-app/api/template.js`):
```javascript
export const config = { runtime: 'edge' };
export default function handler() {
  const kstOffset = 9 * 60 * 60 * 1000;
  const kstDay = Math.floor((Date.now() + kstOffset) / 86400000);
  const templates = [
    'l4ugi3lmvr3en',  // 블랙
    'zxprljfehgozj',  // 블루
    'lxt4xvqnjeaz6',  // 퍼플
  ];
  const template_uuid = templates[kstDay % 3];
  return new Response(JSON.stringify({ template_uuid, day: kstDay, idx: kstDay % 3 }), {
    headers: { 'content-type': 'application/json' },
  });
}
```

### Make.com Data Store 구조 (예정)

```
Key: "set_001" ~ "set_100"
Fields:
  - set_id: number
  - card_1_text: string
  - card_2_text: string
  - card_3_text: string
  - card_4_text: string
  - card_5_text: string
  - card_5_author: string

Key: "current_index"
Fields:
  - value: number (1~100, 매일 +1)
```

---

## 5. 앱 내 리텐션 마케팅

### 쿨다운 자동게시 (매일 9회)

```
cron-job.org (KST 9회) → POST pacers.kr/api/1.1/wf/create_cooldown_post
  → Bubble 워크플로우
  → Claude API (claude-sonnet-4-6)
  → Cooldown_post DB 저장
```

**실행 시간 (KST)**: 06:00 / 07:17 / 09:43 / 11:08 / 12:51 / 14:29 / 16:14 / 18:37 / 20:22

**콘텐츠 규칙**: 블라인드 러닝 채널 감성, 날씨/지명/자기계발어 금지, 1~3줄

### 데일리 퀘스트 (DAU 루프)
```
러닝기록 1회 + 쿨다운 게시 1회 → 무료 티켓 1개
```
→ 티켓으로 "같이 달려요" → 매칭 → 채팅 → 리텐션

### 첫인상 보내기 (Feature 05, 2026-06-20 라이브)
- 티켓 없이 ⚡/🥇/🔥 반응 가능
- 양방향 첫인상 → "같이 달려볼까요?" 유도
- 매칭 전환율 ↑ → 티켓 소모 ↑ → 수익

---

## 6. 성장 퍼널

```
[Instagram 카드뉴스] ─┐
[쿨다운 커뮤니티]    ─┤→ 앱 설치 → 온보딩 → Daily Pacer 매칭
[친구 초대 30티켓]  ─┘        ↓
                              첫인상 보내기
                              ↓
                              "같이 달려요" (티켓 소모)
                              ↓
                     채팅 → 오픈런 참여 → 진성 러너
                              ↓
                     데일리 퀘스트 (DAU 루프)
                              ↓
                         리텐션 ↑, 입소문 ↑
```

---

## 7. KPI & 마일스톤

| 지표 | 현재 (2026-06-21) | 목표 |
|------|-----------------|------|
| 총 회원수 | 1,079명 | **2,000명** |
| DAU | 측정 중 | 200명+ |
| 인스타 팔로워 | - | 5,000명 |
| 매일 인스타 발행 | ✅ 자동화 완료 | 지속 |
| 쿨다운 게시 | 9회/일 자동 | 지속 |
| 친구 초대 전환율 | - | 15%+ |

---

## 8. 작업 이력

| 날짜 | 작업 |
|------|------|
| 2026-06-16 | Daily Pacer 매칭 스코어 라이브 (`api/match.js`) |
| 2026-06-17 | 쿨다운 댓글 딥링크 푸시 / cron-job.org 전환 |
| 2026-06-18 | 쿨다운 예시 116→97개 정제 / 시간대별 프롬프트 / 자동모니터링 Slack 알림 |
| 2026-06-19 | Natively 이주 확정 / RevenueCat 셋업 완료 |
| 2026-06-20 | Feature 05 첫인상 보내기 라이브 / Natively 대시보드 셋업 완료 |
| 2026-06-20 | 인스타 캐러셀 자동화 — 템플릿 3종 로테이션 구현 (`api/template.js`) |
| 2026-06-20 | Sleep 함수 구현 (`api/sleep.js`) — httpbin.org 대체 |
| 2026-06-21 | Story Arc V2 100세트 프리셋 기획 / 노션 마케팅 플랜 정리 |
| 진행중 | 100세트 JSON → Make.com Data Store 연동 |

---

## 9. 다음 작업

- [ ] `running_carousel_100sets_complete.json` 100세트 작성 완료
- [ ] Make.com Data Store에 100세트 업로드
- [ ] Make.com 블루프린트 업데이트 (Module 1-5 → Data Store 방식)
- [ ] Natively 빌드 주문 → TestFlight 검증
- [ ] RevenueCat webhook → Bubble 배선 (`revenuecat_webhook`)
- [ ] 인스타 팔로워 성장 트래킹 시작

---

> 이 파일은 페이서스 마케팅 전략 + 기술 자동화를 통합 관리하는 마스터 문서입니다.
> CLAUDE.md가 개발 문서라면 MARKETING.md는 성장/마케팅 문서입니다.
