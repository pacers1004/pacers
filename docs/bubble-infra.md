# 버블 인프라 & 데이터 구조 상세

---

## 도메인 & API

- **커스텀 도메인**: `pacers.kr`
- **Bubble 원본 도메인**: `pacers-1004.bubbleapps.io`
- **Workflow API**: `https://pacers.kr/version-test/api/1.1/wf`
- **Data API**: `https://pacers.kr/api/1.1/obj/` (live), `version-test/api/1.1/obj/` (dev)
- **Admin API Token**: `36aa51d61c70995e64e40de1f78a3fa6` (환경변수로만 사용, 코드에 하드코딩 금지)
- **인증**: `Authorization: Bearer <token>`

---

## SPA 구조 & URL

페이서스는 `01_main` 단일 SPA. `?tab=` 파라미터로 탭 전환.

| 화면 | URL |
|------|-----|
| 홈 | `/01_main?tab=home` |
| 쿨다운 목록 | `/01_main?tab=cooldown` |
| 쿨다운 상세 | `/01_main?tab=cooldown_detail&cd_id=[post_unique_id]` |
| 알림탭 | `/01_main?tab=notibell` |
| 데일리카드 | `/01_main?tab=daily_card&user_id=[user_id]` |
| 채팅방 | `/04_chat_detail_page/[ChatRoom unique id]` ← SPA 아님, 별도 페이지 |

---

## Privacy & Security

- **Data API**: ✅ 활성화
- **Workflow API**: 활성화
- **X-Frame-Options**: Block all frames (weather-app과 무관, 별도 도메인)
- **GA4**: G-M5JGLNFC7G

---

## OneSignal 푸시 (Natively 플러그인)

### App ID
`e610afff-b159-4c95-8f3c-ffdf86433fb1`

### 핵심 워크플로우 패턴
```
[앱 첫 실행/로그인] → Request push permission
→ OneSignal Player ID Updated 이벤트
→ Make changes User: onesignal_subscription_id = [Player ID]
```

### 푸시 발송 액션 (버블 워크플로우에서)
`Natively - OneSignal - User Single PlayerId - Send Push`
- Title, Message, Redirect URL (딥링크)
- include_player_ids: `receiver's onesignal_subscription_id`

### 딥링크 규칙
| 알림 종류 | Redirect URL |
|----------|-------------|
| 쿨다운 댓글 | `https://pacers.kr/01_main?tab=cooldown_detail&cd_id=[post unique id]` |
| 데일리카드/매거진/오픈런 | `https://pacers.kr/01_main?tab=notibell` |
| 채팅룸 | `https://pacers.kr/04_chat_detail_page/[ChatRoom unique id]` |
| 데일리크루 호감 | `https://pacers.kr/04_chat_detail_page/[ChatRoom unique id]` |

⚠️ **절대 금지**: `https://pacers.kr` 단독 → 앱 대신 외부 브라우저 열림
⚠️ 반드시 전체 URL 사용 (상대경로 `/01_main?...` → iOS 중단)

---

## 버블 DB 주요 타입

### User 핵심 필드
| 필드 | 타입 | 설명 |
|------|------|------|
| `tickets` | number | 매칭 티켓 |
| `today_cards` | List of Users | 당일 추천 카드 |
| `today_match_done` | yes/no | 당일 스코어링 완료 |
| `last_card_refresh` | date | 카드 갱신 시간 |
| `onesignal_subscription_id` | text | 푸시 Player ID |
| `preferred_gender` | text | 모두/이성/동성 |
| `home_address` | geographic | 위치 |
| `tamagotchi_lifetime_km` | number | 냥이 누적 km |
| `tamagotchi_streak` | number | 연속 달리기 일수 |
| `tamagotchi_last_run_date` | date | 마지막 달리기 날짜 |
| `tamagotchi_longest_streak` | number | 최장 스트릭 |

### DailyRunningLog 핵심 필드
| 필드 | 타입 | 설명 |
|------|------|------|
| `date` | date | 날짜 |
| `distance_km` | number | 거리 |
| `duration_sec` | number | 시간(초) |
| `emotion` | EmotionType | happy/proud/tired |
| `comment` | text | 메모 |
| `is_rewarded` | yes/no | 퀘스트 보상 지급 여부 |

### PacerImpression 타입 (첫인상 기능)
| 필드 | 타입 |
|------|------|
| `sender` | User |
| `receiver` | User |
| `impression` | text (pace/runner/consistent) |
| `created_date` | date |
| `is_mutual` | yes/no |

### 전체 타입 목록
**소셜**: Cooldown_post, CooldownComment, CooldownAnonIdentity, Chatmessage, ChatRoom
**러닝**: DailyRunningLog, DailyCrew, DailyCrewInvite, Daily_quest, Daily_weather
**오픈런**: OpenRun_Course, OpenRun_Entry, OpenRun_Group, OpenRun_Ticket_History
**마라톤**: 마라톤 대회, 마라톤 댓글, 마라톤 동행 신청
**챌린지**: PacersChallenge, ChallengeCard, Badge, UserQuest, PacerImpression
**시스템**: Notification, Device, Purchase Attempt, System, Popup, Report

---

## 버블 ↔ iframe 데이터 흐름

- **Single Source of Truth**: 데이터 주인은 항상 버블 DB
- 버블 → iframe: URL 파라미터
- iframe → 버블: `window.parent.postMessage({ type: 'result', data: ... }, '*')`

### JavascripttoBubble 엘리먼트 목록 (01_main)
| 이름 | 역할 | suffix |
|------|------|--------|
| `JavascripttoBubble A` | 스플래시 | bubble_fn_show_splash — 건드리지 말 것 |
| `JavascripttoBubble B` | 매칭 결과 수신 | bubble_fn_match_result (outputlist1 type: text) |
| `JavascripttoBubble 냥이` | 다마고치 기록 | bubble_fn_rlog (output1~4) |
| `JavascripttoBubble openUser` | 랭킹→유저카드 | bubble_fn_openUser (output1 text) — Trigger event=ON 필수 |

---

## Daily Pacer 백엔드 워크플로우

### generate_daily_cards_v5 (버블 백엔드)
- 파라미터: user, limit(70), today_pm12, min_year, max_year
- Step1: today_cards = Search Users (두 Search merge: 10km + 1000km fallback)
  - 조건: 본인제외, 거리내, 유령유저제외(`last_active > -90일`), 좋아요/매칭/viewed 제외, 나이±7
  - Sort: 거리 오름차순 → 최근접속 내림차순
- Step2: last_card_assigned, last_viewed_date, extra_card_reset_time 업데이트
- Step3: viewed_today_list clear

### 00_클로드코드 매칭 스코어_Every time condition
- 조건: today_cards:count > 1 AND today_match_done is no
- Run JS → Vercel /api/match 호출 → bubble_fn_match_result

### JavascripttoBubble B event
- Step1 (count>0): today_cards = Search Users where unique id is in outputlist1
- Step2 (항상): today_match_done = "yes"

---

## 쿨다운 댓글 워크플로우 요약

**프론트 댓글 제출 시** (총 14단계 중 핵심):
- Step1: Create CooldownAnonName (처음 댓글 타인)
- Step2-3: Create CooldownComment (본인 or 신규 타인)
- Step4-5: Notification + OneSignal Push (게시자에게)
- Step6-8: 재댓글 케이스 (기존 익명닉네임 재사용)
- Step14: Schedule API `send_cooldown_author_reply_noti` (게시자 답글 → 기존 댓글러들에게)

**백엔드: send_cooldown_author_reply_noti**
- receiver에게 Notification + Push
- 메시지: "[닉네임]님이 회원님의 댓글에 답글을 남겼어요"

---

## ChottuLink 딥링크

- **서브도메인**: `pacers.chottu.link`
- **Mobile SDK Key**: `c_app_EPZ8ZikKezTni5IsHg1lh5e2aOIAGHqV`
- **용도**: 소셜공유/이메일 등 외부 링크 (앱 미설치 → 앱스토어 이동)
- **OneSignal 푸시**는 ChottuLink 불필요 — BDK/Natively 웹뷰가 직접 처리

---

## 주요 버블 워크플로우

- `generate_daily_cards_v0~v6` — 데일리 카드 (v6 최신)
- `1:1_send_invite` / `1:1_accept_invite` — 1:1 매칭
- `dc_send_invite` / `dc_accept_invite` — 데일리 크루
- `create_cooldown_post_v2` — 쿨다운 자동 게시 (v1 삭제 가능)
- `send_cooldown_author_reply_noti` — 쿨다운 댓글 알림
- `revenuecat_webhook` — 티켓 지급 (§27 참조)
