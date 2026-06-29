# PACERS 프로젝트 마스터 문서

> 디자인 원칙 → [`DESIGN.md`](./DESIGN.md) | 마케팅 → [`MARKETING.md`](./MARKETING.md)

## 1. 프로젝트 정체성
- **서비스명**: 페이서스(Pacers) — 러닝메이트 매칭 및 소셜 러닝 플랫폼
- **핵심 철학**: "멀리, 빨리 뛰는 법보다 오늘도 운동화를 다시 신는 법을 제안합니다."
- **현재**: 국내 버블앱 순위 19위, 유저 1,079명 라이브 서비스
- **목표**: 국내 1위 → 세계 1위 러닝 플랫폼
- **경쟁 앱**: 나이키런클럽, 스트라바
- **역할**: 너는 페이서스의 수석 개발자다. 페이서스의 성공을 위해 창의적으로 사고하고 능동적으로 제안해라.

---

## 2. 기능 파이프라인

| # | 기능명 | 로컬 | GitHub | 배포 | 상태 |
|---|--------|------|--------|------|------|
| 01 | Today Weather | `~/weather-app` | `pacers1004/weather-app` | Vercel | ✅ 라이브 |
| 02 | Cooldown Auto Post | `~/weather-app` | `pacers1004/weather-app` | Vercel + cron-job.org | ✅ 라이브 |
| 03 | Daily Pacer 매칭 스코어 | `~/weather-app` | `pacers1004/weather-app` | Vercel | ✅ 라이브 |
| 04 | 자동 모니터링 & Slack | `~/weather-app` | `pacers1004/weather-app` | Vercel | ✅ 라이브 |
| 05 | Daily Pacer 첫인상 | 버블 전용 | — | Bubble | ✅ 라이브 (2026-06-20) |
| 06 | 마라톤 훈련 플랜 | `~/weather-app` | `pacers1004/weather-app` | Vercel | ⚠️ 미완성 (버블 연동 미완) |

**배포 선택**: UI 웹앱 → Vercel / 스케줄러 → GitHub Actions / 실시간 → Railway

---

## 3. 기술 스택

- **메인**: Bubble.io (Web) + **Natively** (앱스토어, BDK→Natively 이주 진행중 §19)
- **커스텀 기능**: HTML/JS/CSS → Vercel 배포 → 버블 HTML 엘리먼트에 iframe 삽입
- **배포**: `git push origin main` → Vercel 자동 배포
- **API KEY**: 환경변수만 (하드코딩 절대 금지)

### 개발환경 (Mac, 2026-06-19 기준)
| 도구 | 버전 | 비고 |
|------|------|------|
| Node.js | v24.17.0 | `/usr/local/bin/node` |
| Vercel CLI | v54.14.2 | `~/.npm-global/bin/vercel` ← Claude Code에서 전체 경로 필요 |
| Python | 3.14.6 | `/Library/Frameworks/Python.framework/Versions/3.14/bin/python3` |
| Python anthropic SDK | v0.111.0 | pip3 설치됨 |

**Vercel 계정**: `bimhara-9117` / 프로젝트: `pacers-projects1004/weather-app` (재로그인 불필요)

---

## 4. 개발 원칙

### ✅ Auto Mode 기준 (2026-06-20 확정)
| 구분 | 처리 |
|------|------|
| 파일 수정, git push, Vercel 배포, JSON/MD 수정 | 바로 진행 |
| DB 삭제, 라이브 코드 대규모 변경 | **반드시 확인 후** |
| 기능 추가 여부, 전략 결정 | 여쭤봄 |

### 🚫 최우선: 할루시네이션 금지
추측·상상을 사실처럼 말하지 않는다. 모르면 "모른다"고 한다.
- 팩트 → 출처 제시 / 추측 → "추정입니다" 명시 / 모름 → "확인 필요"
- 코드·버블 필드·워크플로우는 **직접 확인 후** 답한다 (기억/상상 금지)
- **Why**: 유저 1,079명 라이브 서비스 의사결정을 내가 준 정보로 함 → 지어낸 정보 = 잘못된 배포

### 기타 원칙
- 파일 수정 시 최소한만 변경 / 불필요한 파일 읽기 금지
- UI/디자인/색상 건드리지 말 것 (명시적 요청 없으면)
- 버블 DB와 iframe 간 데이터 무결성 최우선
- 버블 워크로드 폭탄 요금 방지 — 리소스 효율적 사용

### API 크레딧 모니터링
- `api/cooldown.js` — claude-sonnet-4-6, 4회/일 (월 1회 Anthropic Console 확인)
- `api/match.js` — Claude API 미사용 (순수 알고리즘)

### ⚠️ iframe Failsafe (모든 iframe 삽입 시 필수)
에러 화면이 유저에게 노출되는 상황 절대 불허.
- **Level 1**: 미니앱 내부 API 실패 → 미니앱 자체 retry/fallback
- **Level 2**: HTML 파일 자체 로드 실패 → 버블에서 로딩 오버레이 + 5초 재시도 3회

```html
<!-- 버블 HTML 엘리먼트 -->
<div id="wrap" style="position:relative;">
  <div id="loading-overlay" style="position:absolute;inset:0;background:#000;z-index:10;
       display:flex;align-items:center;justify-content:center;border-radius:5px;">
    <div style="width:22px;height:22px;border:2.5px solid rgba(255,255,255,0.12);
         border-top-color:#3764f1;border-radius:50%;animation:sp 0.8s linear infinite;"></div>
    <style>@keyframes sp{to{transform:rotate(360deg)}}</style>
  </div>
  <iframe id="mini-frame" src="[URL]" style="width:100%;height:100%;border:none;"></iframe>
</div>
<script>
(function(){
  var loaded=false,retries=0;
  window.addEventListener('message',function(e){
    if(e.data&&e.data.type==='[앱명]_loaded'){
      loaded=true;document.getElementById('loading-overlay').style.display='none';
    }
  });
  function retry(){
    if(loaded||retries>=3)return;retries++;
    var f=document.getElementById('mini-frame'),s=f.src;
    f.src='';setTimeout(function(){f.src=s;},300);
    if(retries<3)setTimeout(retry,5000);
  }
  setTimeout(retry,5000);
})();
</script>

<!-- 미니앱 HTML 내부 (로드 성공 신호) -->
<script>window.parent.postMessage({type:'[앱명]_loaded'},'*');</script>
```

---

## 5. 디자인 시스템

- **컬러**: 블랙/화이트 베이스, 포인트 `#3764F1`
- **폰트**: Noto Sans KR (한글), Noto Serif (영문)
- **스크린샷 레퍼런스**: `~/pacers/screenshots/`

### NRC 디자인 원칙
- 숫자가 곧 디자인: 핵심 지표를 극도로 크고 굵게 (hero 요소)
- 라벨은 숫자 아래 작게 / 장식 최소화, 데이터만 남김 / 충분한 여백
- 다크모드: 순수 블랙 + 순수 화이트 / 라이트모드: 반전

---

## 6. 버블 앱 인프라

### 도메인
- **커스텀**: `pacers.kr` / **Bubble 원본**: `pacers-1004.bubbleapps.io`
- **Workflow API**: `https://pacers.kr/version-test/api/1.1/wf`
- **Data API**: 비활성화 / **Workflow API**: 활성화

### 앱 URL 구조 (SPA)
`01_main` 단일 페이지, `?tab=` 파라미터로 화면 전환 (페이지 로드 없음)

| 화면 | URL |
|------|-----|
| 홈 | `/01_main?tab=home` |
| 쿨다운 목록 | `/01_main?tab=cooldown` |
| 쿨다운 상세 | `/01_main?tab=cooldown_detail&cd_id=[post_id]` |
| 알림탭 | `/01_main?tab=notibell` |
| 채팅방 | `/04_chat_detail_page/[ChatRoom_unique_id]` (별개 페이지, SPA 아님) |

### 푸시 알림 딥링크 (2026-06-20 전체 완료 ✅)
| 알림 | URL |
|------|-----|
| 쿨다운 댓글 | `https://pacers.kr/01_main?tab=cooldown_detail&cd_id=[post_id]` |
| 데일리카드/오픈런/매거진 | `https://pacers.kr/01_main?tab=notibell` |
| 채팅룸 | `https://pacers.kr/04_chat_detail_page/[ChatRoom_id]` |

**⚠️ OneSignal Launch URL 규칙**: 비워두기(홈이동) or 전체URL만 / `pacers.kr` 단독 입력 → 외부 브라우저 열림(절대 금지)
**⚠️ 딥링크 URL 반드시 전체 URL 사용** (`/01_main?...` 상대경로 → iOS 중단)

### ChottuLink 딥링크 (외부 공유용만)
- 서브도메인: `pacers.chottu.link` / SDK Key: `c_app_EPZ8ZikKezTni5IsHg1lh5e2aOIAGHqV`
- 소셜 공유/이메일/SMS 용 (앱 미설치 → 앱스토어)

### 식별자
- GA4: `G-M5JGLNFC7G`
- OneSignal App ID: `e610afff-b159-4c95-8f3c-ffdf86433fb1`
- Bundle ID: `com.pacers.pacers` (iOS/Android 동일)
- App Store ID: `6754361646`

---

## 7. 버블 데이터 타입

**소셜**: `Cooldown_post`, `CooldownComment`, `CooldownAnonIdentity`, `Chatmessage`, `ChatRoom`
**러닝**: `DailyRunningLog`, `DailyCrew`, `DailyCrewInvite`, `Daily_quest`
**오픈런**: `OpenRun_Course`, `OpenRun_Entry`, `OpenRun_Group`
**마라톤**: `마라톤 대회`, `마라톤 댓글`, `마라톤 동행 신청`
**챌린지**: `PacersChallenge`, `Badge`, `UserQuest`
**시스템**: `Notification`, `Device`, `Purchase Attempt`, `System`, `Popup`, `Report`
**신규**: `PacerImpression` (첫인상, 2026-06-20)

---

## 8. 버블 ↔ iframe 데이터 연동

- **Single Source of Truth**: 데이터 주인은 항상 버블 DB
- 버블 → iframe: URL 파라미터 (`?lat=&lon=` 등)
- iframe → 버블: `window.parent.postMessage({ type: 'result', data: ... }, '*')`
- 영구 데이터는 반드시 버블 DB 저장

---

## 9. 앱 화면 구조

하단 네비: 홈 / 마라톤 / 크루 / 쿨다운 / 채팅 / 마이

| 화면 | 핵심 요소 |
|------|----------|
| 홈 | 로고+위치+알림 / 오픈런배너 / Today Weather / Daily Pacer |
| Daily Pacer 상세 | 페이서 사진+닉네임+페이스 / 프로필 / "같이 달려요"(티켓) / 첫인상(⚡🥇🔥) |
| 오픈런 | 지역필터 / 코스카드 / 페이스별 그룹 (Beta 무료) |
| 쿨다운 | 카테고리 / 익명 게시글 / "+글쓰기" 플로팅 |
| 마이 | 프로필 / 티켓충전 / 뱃지/챌린지 / 데일리퀘스트 |

**티켓 시스템**: 매칭 신청 → 소모. 무료: 퀘스트(1티켓)/초대(30). 유료: 인앱결제 (450원/티켓)

---

## 10. 핵심 기능 요약

- **Today Weather**: `weather-app-pied-theta.vercel.app/card.html` (러닝 지수 기반)
- **Cooldown Auto Post**: cron-job.org → `/api/cooldown` → Claude API → Bubble (4회/일)
- **Daily Pacer**: 위치 기반 매칭 + 스코어링 (`/api/match`) (매일 12시 갱신)
- **마라톤 캘린더**: 전국 대회 + D-day + 동행 매칭
- **오픈런**: 큐레이션 그룹 러닝 (매주 토요일)
- **데일리 퀘스트**: 러닝기록 + 쿨다운 → 무료 티켓 → 리텐션 루프

---

## 11. Feature 03: Daily Pacer 매칭 (라이브 ✅)

**파일**: `~/weather-app/api/match.js`

**버블 데이터 흐름**:
```
Page is loaded → generate_daily_cards_v5 (조건: last_card_refresh < cycle_start)
  → today_cards = Search Users (10km 거리순 + 1000km fallback, limit 150)
  → today_match_done = "no"

"Do every time" (today_cards:count > 1 AND today_match_done is no)
  → Toolbox Run JS → POST /api/match → top2 IDs
  → JavascripttoBubble B → today_cards 업데이트 → today_match_done = "yes"

rg_recomend_DailyPacer → today_cards:items until #2 (프론트에서 성별 필터)
```

**매칭 스코어링**:
- 이성/동성: 거리75% + 나이25%
- 모두: 거리65% + 성별20% (이성=100/동성=50) + 나이15%
- 거리점수: `Math.max(5, Math.round(100 - km×3))` (선형, 동점 방지)
- 나이점수: 3살=100, 5살=80, 7살=60, 9살=40, 10살+=25
- Failsafe: "모두" top2가 동성이면 이성 1명 강제 삽입

**버블 필드**: `today_cards`, `today_match_done`, `last_card_refresh`
**버블 워크플로우**: `00_클로드코드 매칭 스코어_Every time condition`, `JavascripttoBubble B event`, `generate_daily_cards_v5`
**Failsafe**: API 실패 → `outputlist1: []` → today_cards 기존 유지 → today_match_done="yes" (무한 재시도 방지)

---

## 12. Feature 02: Cooldown Auto Post

**실제 라이브 흐름**:
```
cron-job.org (4회/일: 8·12·18·21시 KST)
  → POST https://weather-app-pied-theta.vercel.app/api/cooldown
  → examples.json(148개) + 페르소나(9종) + getFormatForSlot() 유형 배정
  → Claude API (sonnet-4-6) 글 생성 → hasAISmell 필터 → Haiku QA (최대 3회)
  → Bubble create_cooldown_post_v2
```

**cron-job.org**: `bimhara@gmail.com` / API Key: `6rYWbCOR6oIWjQDRGtJAwIFSzwgY39CAsm3HjRku6hY=`
**⚠️ 60일 미로그인 시 계정 비활성화 → 2달마다 console.cron-job.org 접속 필요**

**글 유형 분산**: `getFormatForSlot()` — 날짜 시드로 7개 유형 셔플 (후기/한줄잡담/질문/장비/루틴/자랑/고민)
**examples.json 업데이트**: `~/weather-app/api/examples.json`만 수정 (cooldown-scheduler 레포는 사용 안 함)

**금지 규칙**: 날씨/계절/날짜/지명/대회명/음주/해장/새벽런/야간런/격식체/자기계발어

**환경변수**: `ANTHROPIC_API_KEY`, `BUBBLE_API_TOKEN`=`36aa51d61c70995e64e40de1f78a3fa6`

---

## 13. 수익 모델 + 페이서스 2.0

**현재**: 인앱 결제 (티켓 시스템, 1티켓=450원)

**페이서스 2.0 확정 (2026-06-22)**:
- 구독 모델 신설 (월 5,500~7,800원, 가격 미확정) + 티켓 모델 공존
- 구독자: 호감보내기·데일리크루·오늘의카드·마라톤훈련플랜 등 전부 무제한
- 티켓 인상: 호감보내기 3→5~6티켓 (구독 유도용)
- "행운의 7%" 캠페인: 구독료 7% → 포캣멍센터 후원
- **구현 방식**: BDK 기반 (Natively 이주 보류, 가우라브 BDK 구독 템플릿 대기)

**미확정**:
- [ ] 정확한 구독 가격 / 티켓 인상값
- [ ] RevenueCat 구독 SKU + webhook RENEWAL/CANCELLATION/EXPIRATION 처리
- [ ] Bubble User에 `is_subscriber` 필드 + 모든 티켓 차감 워크플로우 구독자 분기

---

## 14. 버블 백엔드 주요 워크플로우

- `generate_daily_cards_v6` — 데일리 카드 생성 (최신)
- `1:1_send_invite` / `1:1_accept_invite` — 1:1 매칭
- `dc_send_invite` / `dc_accept_invite` — 데일리 크루
- `create_cooldown_post_v2` — 쿨다운 자동 게시 (v1 삭제 가능)
- `send_cooldown_author_reply_noti` — 게시자 댓글 시 기존 댓글러들에게 알림
- `revenuecat_webhook` — RevenueCat 결제 검증 (미구현, §19 참고)

---

## 15. 쿨다운 댓글 버블 워크플로우

**익명 구조**: 본인→게시글 닉네임 재사용 / 타인 첫댓글→CooldownAnonName 생성 / 재댓글→기존 닉네임

**댓글 제출 주요 단계**:
| 단계 | 조건 | 액션 |
|------|------|------|
| Step 1 | 타인 + 처음 댓글 | Create CooldownAnonName |
| Step 3 | 타인 + 처음 댓글 | Create CooldownComment + 알림 + 푸시 |
| Step 6 | 타인 + 재댓글 | Create CooldownComment (기존 닉네임) + 알림 + 푸시 |
| Step 9/10 | — | Cooldown_post에 댓글/댓글러 추가 |
| Step 14 | 게시자가 댓글 | send_cooldown_author_reply_noti (기존 댓글러들에게) |

**푸시**: 현재 BN-Push → Natively 이주 후 `OneSignal - User Single PlayerId - Send Push` 교체 예정
**딥링크**: `https://pacers.kr/01_main?tab=cooldown_detail&cd_id=[post_id]`

---

## §19: Natively 이주 현황

**완료**:
- ✅ Natively 공식 확인: 소비성 IAP / 기존 Bundle ID 유지 / OneSignal / 카메라 모두 지원
- ✅ RevenueCat 셋업: 프로젝트 de75c31e, iOS `appl_` / Android `goog_` Public 키 발급
- ✅ Natively 대시보드 전체 설정 완료 (Bundle ID `com.pacers.pacers`, OneSignal, RevenueCat)
- ✅ Natively 플러그인 v2.30.0 버블에 설치됨

**🔴 다음 세션 시작점 — Bubble Dev 배선 (본게임)**:
1. Natively 빌드 주문 (Unlimited 트라이얼 $32/월) → TestFlight/내부테스트 설치
2. 🔴 RevenueCat 결제: 로그인 시 `Set Customer ID` → 구매 시 `Purchase Package` → 🔒 티켓 지급은 반드시 webhook으로
3. 🟡 OneSignal 푸시: BN-Push → `OneSignal - User Single PlayerId - Send Push` 교체
4. 🟡 스플래시: BN-Remove-loading → `Set Error Handler` (Natively-Device) 교체
5. 🔴 TestFlight 샌드박스 결제 E2E 검증
6. 검증 완료 → 같은 Bundle ID로 스토어 업데이트 제출

**Natively 대시보드 핵심 설정값**:
- App URL: `https://pacers.kr/00_sign_up_sign_in_step1_page`
- Bundle ID: `com.pacers.pacers` (**⚠️ 바꾸면 기존 유저 못 이어받음!**)
- iPad: ON 유지 (Apple QA1623, 끄면 업로드 거부)
- Service Worker: ON (Domain `pacers.kr`) / Pull to Refresh: ON

**미해결 버그 (BDK 이주로 해소 기대)**:
- iOS 키보드 + 댓글 입력창 (Growth 플랜 $29/월 필요)
- Android 콜드 스타트 흰 화면
- Android 버블 하단탭이 네비바 가림 → `body { padding-bottom: env(safe-area-inset-bottom); }`
- 날씨 카드 좌표 업데이트 안 됨 (주소 변경 WF에 postMessage 추가 필요)

---

## §26: Natively 플러그인 핵심 레퍼런스

**01_main에 추가할 엘리먼트**:
- `Natively - Purchases` (RevenueCat 인앱결제)
- `Natively - Push Notifications (OneSignal)` (푸시)
- `Natively - Device` (스플래시/로딩)
- `Natively - ATT` (iOS 추적 동의, 권장)

### BDK → Natively 교체 매핑
| BDK 액션 | Natively 대체 |
|----------|--------------|
| BN-Purchase | Purchase Package (Set Customer ID 선행 필요) |
| BN-Push (단일) | OneSignal - User Single PlayerId - Send Push |
| BN-Remove-loading | Set Error Handler (Natively - Device) — TestFlight 검증 필요 |
| BDK 권한 요청 | Request Push Notification Permission |
| BDK Player ID 저장 | OneSignal Player ID Updated 이벤트 → DB 저장 |

### RevenueCat 결제 흐름
```
로그인 → Set Customer ID (= Current User's unique id)
구매버튼 → Purchase Package (Package ID = "com.pacers.pacers.12" 등)
🔒 실제 티켓 지급: RevenueCat webhook → Bubble revenuecat_webhook
  → event_type = "INITIAL_PURCHASE" → Search User → tickets += N
  → product_id 매핑: .12=12 / .30=30 / .50=50 / .100=100 / .200=200 / .500=500 / .700=700 / .1000=1000
Purchase Success 이벤트 → UI 갱신만 (티켓 지급 아님, JS 콜백 믿으면 해킹!)
```

**webhook URL**: `https://pacers.kr/api/1.1/wf/revenuecat_webhook` (Bearer BUBBLE_API_TOKEN)

### OneSignal 푸시 배선
```
앱 첫 실행 → Request Permission → Permissions Authorized
→ Set External ID (= User unique id)
→ Get Player ID → Player ID Updated → DB 저장 (onesignal_subscription_id)

푸시 발송 액션:
→ OneSignal - User Single PlayerId - Send Push
  Title / Message / Redirect URL (딥링크)
```

---

## §28: Feature 05 — Daily Pacer 첫인상 (라이브 ✅, 2026-06-20)

- **기능**: 티켓 없이 ⚡페이스딱맞겠다 / 🥇진짜러너다 / 🔥꾸준함이보여 전송
- **DB**: `PacerImpression` (sender, receiver, impression, created_date, is_mutual)
- **리셋**: 매일 정오 12시 자동 (Daily Pacer 사이클과 동일)
- **버블 컨디셔널**: 내가 보냄 → "전달했어요" / 상호 → "서로 보냈어요 + 같이 달려볼까요?"
- **애니메이션**: `grp_러너 첫인상 내부` HTML D 엘리먼트 → Toolbox Run JS로 `fireImpression('⚡')` 호출

---

## §30: Feature 06 — 마라톤 훈련 플랜 (⚠️ 버블 연동 미완)

**파일**: `~/weather-app/marathon-plan.html` + `~/weather-app/api/marathon.js`
**URL**: `https://weather-app-pied-theta.vercel.app/marathon-plan.html`
**테스트**: `?user_id=test&race_date=2026-11-15&race_name=서울마라톤&race_distance=full&level=intermediate&current_weekly_km=40&today=2026-06-21&rlog_this_week=18`

**완료**: UI + API 알고리즘 (10%룰 + 3:1사이클 + 테이퍼링, 24주, 5k~full 거리별 피크)

**미완성**:
- ⏳ 마라톤 캘린더 대회 선택 → iframe URL 파라미터 자동 주입
- ⏳ 훈련 체크인 → DailyRunningLog 연동
- ⏳ 스트릭 / 플랜 저장 → 버블 DB 연동 (현재 localStorage만)

---

## §31: 구독 모델 (방향 확정, 구현 미착수)

→ §13 수익 모델 참고

---

## §32: 마케팅 자동화

> 상세 → `MARKETING.md` | 릴스 영상: `python3 "/Users/janghara/AUTO VIDEO/make_reel.py" --mode epic_45`

| 시나리오 | 시간 | 내용 |
|---------|------|------|
| Make.com 6263718 | 22:00 KST | 스토리 아크 5장 (`/api/card-news?arc=1`) |
| Make.com 6270177 | 19:30 KST | 요일별 러닝 팁 5장 (`/api/news-cards`) |
| Make.com 6322461 | 22:30 월수금 | 클투스타일 7장 (`/api/card-news`) |

> ⚠️ 6270177: 이름은 "뉴스 카드뉴스"이나 실제 RSS 미사용 — Sonnet 사전생성 풀에서 읽기

**Placid 템플릿**: 블랙(`l4ugi3lmvr3en`)/블루(`zxprljfehgozj`)/퍼플(`lxt4xvqnjeaz6`) — 3일 사이클

### 카드 풀 아키텍처 (2026-06-29 도입)
실시간 Claude 호출 대신 오프라인 사전생성 → JSON 풀 → API 읽기
- `arc_cards_pool.json` — 80세트 (스토리 아크 5장)
- `tip_cards_pool.json` — 67세트 (팁 + 비교 카드)
- `news_cards_pool.json` — 105개 (요일당 15개 × 7일)
- 생성 스크립트: `python3 "/Users/janghara/AUTO VIDEO/gen_cards.py" all`
- 풀 위치: `~/weather-app/api/pools/`
- **콘텐츠 방향**: "가르치려는 압박" → "공감→인정→희망" (이미 하고 있었다)
- 풀 소진 시 → 실시간 Claude Sonnet 폴백

---

## §33: 페이서 다마고치 (기획 단계)

달리기 습관 → 캐릭터 성장. DailyRunningLog 연동. 홈화면 iframe.

**레벨**: Lv0 알(0km) → Lv1 아기냥(10) → Lv2 새벽냥(30) → Lv3 뚱냥이(80) → Lv4 페이서냥(200) → Lv5 엘리트냥(500) → Lv6 마라토너냥(1000)
**상태**: happy(오늘달림) / normal(1일) / sad(2일) / danger(3일+) / egg(7일+, 레벨-1)
**파일**: `cat.html` (홈카드) + `cat_detail.html` (상세)
**버블 신규 필드 3개**: `tamagotchi_level`, `tamagotchi_streak`, `tamagotchi_last_run_date`
**노션**: https://app.notion.com/p/387636bbf08a81899380d218c3d03d24

---

## Bubble DB 구조 (2026-06-24 Data API 직접 확인)

**API**: `https://pacers.kr/version-test/api/1.1/obj/` (Auth: Bearer BUBBLE_API_TOKEN)

### User 핵심 필드
| 필드 | 설명 |
|------|------|
| `tickets` | 티켓 수 |
| `today_cards` | 당일 추천 카드 목록 |
| `today_match_done` | 스코어링 완료 여부 |
| `last_card_refresh` | 카드 갱신 시간 |
| `last_active` | 최근 활동 |
| `home_address` / `home_address_kor` | 주소 |
| `preferred_gender` | 선호 성별 |
| `gender` / `birth_year` | 성별/생년 |
| `onesignal_subscription_id` | OneSignal Player ID |
| `badges` / `invite_code` | 뱃지/초대코드 |
| `viewed_ids` / `viewed_today_list` | 조회 기록 |
| `paceRange` / `race_experience` | 러닝 스타일 |

### DailyRunningLog
`date` / `distance_km` / `duration_sec` / `emotion` / `comment` / `is_rewarded` / `week_key`

### Cooldown_post
`cd_title` / `cd_category` / `cd_post_author` / `cd_is_anonymous` / `cd_an_nickname` / `cd_post_likes_count` / `cd_comments` / `cd_commenters` / `cd_views` / `cd_is_visible`

### 마라톤 대회 (API명: `%EB%A7%88%EB%9D%BC%ED%86%A4%20%EB%8C%80%ED%9A%8C`)
`마라톤 대회명` / `대회 날짜` / `거리 종류` / `지역` / `집결 장소` / `집결 시간` / `접수 상태` / `접수 마감일_date` / `노출 여부`

### OpenRun
- **Course**: `Main_Title`, `Course_Distance`, `Course_Region`, `Meeting_Point`
- **Group**: `Members`, `Match_Date`, `Linked_Course`
- **Entry**: `Applicant_User`, `Match_Status`, `Is_Leader`

---

## 버전 백업 규칙
라이브 iframe HTML 크게 바꾸기 전: `cp` → `archive/<name>_v<N>.html` → `~/pacers/VERSIONS.md` 한 줄 추가 → push
