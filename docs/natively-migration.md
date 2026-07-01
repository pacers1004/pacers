# BDK → Natively 이주 상세

> **상태**: Natively 대시보드 셋업 전체 완료 ✅. 다음 = Bubble Dev 배선 (본게임).
> **BDK**: 사실상 방치/지원중단 상태. 라이브 앱(1,125명) 의존 → 사업 연속성 리스크.

---

## 확정 사항

- **이주 대상**: Natively (RevenueCat 소비성 IAP 지원 확인 완료)
- **Bundle ID 유지**: `com.pacers.pacers` → 기존 유저/리뷰/IAP SKU 유지
- **OneSignal**: 기존 셋업 그대로 재사용 (Natively도 OneSignal 지원)
- **RevenueCat**: 셋업 완료 (appl_/goog_ Public 키 입력 완료)

---

## RevenueCat 셋업 현황

| 항목 | 상태 |
|------|------|
| 프로젝트 ID | de75c31e |
| iOS P8 키 (Key ID 62Y9BJDJQN) | ✅ Valid, appl_ Public 키 발급 |
| Android 서비스 계정 | revenuecat@pacers-456310.iam.gserviceaccount.com, goog_ Public 키 발급 |
| Secret API Key V1 | Bubble 플러그인 `revenuecat_apiKey` 입력 완료 |
| 티켓 상품 8종 (com.pacers.pacers.12~1000) | 소비성(One-Time Purchase) 확인 |

**키 위치**:
- `appl_`(iOS) + `goog_`(안드) Public 키 → **Natively 대시보드** (Features→In-app Purchases)
- Secret V1 키 → **Bubble 플러그인** `revenuecat_apiKey` (서버사이드용)

---

## Natively 대시보드 셋업 (완료)

| 항목 | 값 |
|------|-----|
| App URL | `https://pacers.kr/00_sign_up_sign_in_step1_page` |
| Bundle ID | `com.pacers.pacers` |
| Package Name | `com.pacers.pacers` |
| App Name | 페이서스 |
| iPad support | **ON 유지** (Apple QA1623 — 끄면 업로드 거부) |
| OneSignal App ID | `e610afff-b159-4c95-8f3c-ffdf86433fb1` |
| Push 알림 | ON |
| IAP (RevenueCat) | ON, appl_/goog_ Public 키 입력 |
| Service Worker | ON, Domain: `pacers.kr` |
| Apple ATT | OFF (광고/인앱분석 미사용) |
| 요금제 | Unlimited($32/월) 14일 트라이얼 권장 |

---

## Bubble 플러그인 엘리먼트 매핑

### BDK → Natively 액션 교체

| BDK 액션 | Natively 대체 |
|----------|--------------|
| BN-Purchase | Purchase Package (Natively - Purchases) |
| BN-Push (단일) | OneSignal - User Single PlayerId - Send Push |
| BN-Remove-loading | Set Error Handler (Natively - Device) — TestFlight 검증 필수 |

### 인앱결제 (Natively - Purchases)

**핵심 워크플로우 패턴**:
```
[로그인 완료] → Set Customer ID (= Current User's unique id)
[구매 버튼] → Purchase Package (Package ID = com.pacers.pacers.12 등)
[Purchase Success 이벤트] → Show alert "충전 완료!" (UI만)
⚠️ 실제 티켓 지급은 RevenueCat webhook → Bubble 백엔드
```

### 티켓 수량 매핑

| Product ID | 티켓 |
|---|---|
| com.pacers.pacers.12 | 12 |
| com.pacers.pacers.30 | 30 |
| com.pacers.pacers.50 | 50 |
| com.pacers.pacers.100 | 100 |
| com.pacers.pacers.200 | 200 |
| com.pacers.pacers.500 | 500 |
| com.pacers.pacers.700 | 700 |
| com.pacers.pacers.1000 | 1000 |

---

## RevenueCat Webhook 워크플로우 설계

**Bubble 백엔드 API 워크플로우**: `revenuecat_webhook`
- Endpoint: `https://pacers.kr/api/1.1/wf/revenuecat_webhook`
- 파라미터: event_type(text), product_id(text), app_user_id(text)
- Step1 (Only when event_type = "INITIAL_PURCHASE" or "NON_SUBSCRIPTION_PURCHASE"):
  - Search Users where unique id = app_user_id
  - Make changes: tickets += [product_id에 따른 티켓 수]

⚠️ **보안 필수**: JS 콜백만 믿으면 공짜 티켓 해킹 가능. 반드시 webhook으로 지급.

---

## 다음 세션 작업 순서 (Bubble Dev 배선)

1. 빌드 1회 주문 (iOS/Android) → TestFlight/내부테스트 설치
2. 🔴 RevenueCat 결제 배선 (Set Customer ID + Purchase Package + webhook)
3. 🟡 OneSignal 푸시 배선 (BN-Push → Natively 교체)
4. 🟡 스플래시/로딩: BN-Remove-loading → Set Error Handler (Natively - Device)
5. 🔴 샌드박스 결제 검증 (TestFlight + 샌드박스 계정)
6. 검증 완료 → 같은 Bundle ID로 스토어 업데이트 제출

---

## Bubble 플러그인 전체 엘리먼트 (페이서스 사용 기준)

| 엘리먼트 | 용도 |
|----------|------|
| Natively - Push Notifications (OneSignal) | 푸시 알림 ✅ 핵심 |
| Natively - Purchases | RevenueCat 인앱결제 ✅ 핵심 |
| Natively - Device | 스플래시/에러 핸들링 ✅ 핵심 |
| Natively - ATT | iOS 추적 동의 (OFF 유지, 광고 미사용) |
| Natively - Camera | 프로필 사진 |
| [DEPRECATED] Natively - Push Notifications (Klaviyo) | 사용 금지 |
| [DEPRECATED] Natively - Contacts (구) | 사용 금지 |
