# 기타 기능 상세 문서

---

## Feature 03: Daily Pacer 매칭 스코어

> **상태**: ✅ 2026-06-16 라이브 배포 완료
> **파일**: `~/weather-app/api/match.js`

### 스코어링 가중치

**이성/동성 선택**:
| 요소 | 가중치 |
|---|---|
| 거리 | 75% |
| 나이차 | 25% |

**모두 선택**:
| 요소 | 가중치 |
|---|---|
| 거리 | 65% |
| 성별 (이성=100, 동성=50) | 20% |
| 나이차 | 15% |

**모두 Failsafe**: top2가 모두 동성이면 → 스코어 최고점 이성 1명 강제 삽입

### 거리 점수 (선형 연속값)
`getDistScore(km) = Math.max(5, Math.round(100 - km × 3))`

### 나이차 점수
3살 이내=100 / 5살=80 / 7살=60 / 9살=40 / 10살+=25

### Toolbox 입력 파라미터 (버블에서)
param1~4: 나의 위도/경도/생년/성별 + param5: preferred_gender
paramlist1~5: 후보 ID목록/위도/경도/생년/성별 (최대 70명)

---

## Feature 05: Daily Pacer 첫인상 보내기

> **상태**: ✅ 2026-06-20 라이브

- **기능**: 티켓 없이 상대에게 첫인상 전달 (⚡페이스딱맞겠다 / 🥇진짜러너다 / 🔥꾸준함이보여)
- **DB 타입**: `PacerImpression` (sender, receiver, impression, created_date, is_mutual)
- **리셋**: 데일리 카드 사이클(매일 정오)과 동일
- **버튼 탭 애니메이션**: 이모지 파티클 6개 + 그룹 슬라이드업 (`body.imp-firing` pre-hide 패턴)
- **리텐션 임팩트**: 저마찰 인터랙션 → 워밍업 → 매칭 성사율 ↑ → 티켓 소모 ↑

---

## Feature 06: 마라톤 훈련 플랜

> **상태**: ⚠️ UI + API 완성. 버블 연동 미완성. **목표: 7월 중순 라이브**
> **파일**: `~/weather-app/marathon-plan.html` + `~/weather-app/api/marathon.js`
> **배포 URL**: `https://weather-app-pied-theta.vercel.app/marathon-plan.html`

### URL 파라미터 (버블에서 주입)
`?user_id=&race_date=YYYY-MM-DD&race_name=&race_distance=full&level=intermediate&current_weekly_km=40&today=YYYY-MM-DD&rlog_this_week=18`

### api/marathon.js (완성)
- 순수 알고리즘 (외부 API 없음)
- 10% 룰 + 3:1 사이클 + 테이퍼링
- 거리별 피크 km: 5k=35, 10k=50, half=65, full=80
- 최대 24주 플랜

### 미완성 항목
- ⏳ 마라톤 캘린더 대회 선택 → iframe URL 자동 주입
- ⏳ 훈련 체크인 → 버블 DailyRunningLog 연동
- ⏳ 스트릭 데이터 → 버블 DB 읽기
- ⏳ 플랜 저장 → 버블 DB (현재 localStorage만)

---

## Feature 운세카드 (사주/타로)

> ⏸️ **HOLD (2026-06-24)**: 마라톤+다마고치 우선. 재개 시 AUDIT F5 보안 이슈 처리 필요.
> **파일**: `fortune.html` + `fortune-card.html` + `api/fortune.js`

### 보안 이슈 (재개 시 처리)
- `/api/fortune` 엔드포인트에 인증 없음 → 직접 호출 가능
- iframe → API 직접 호출 시 티켓 차감 우회 가능
- **해결책**: postMessage 경유 → 버블 워크플로우에서 티켓 차감 후 API 호출

---

## Daily Pacer 매칭 버블 워크플로우 상세

### generate_daily_cards_v5 핵심 필터
- `unique id <> user` (본인 제외)
- `home_address within 10km` + 1000km fallback
- `1:1 likes/matched_users doesn't contain user`
- `unique id isn't in viewed_ids`
- `birth_year ≥ min_year` AND `≤ max_year` (나이 ±7년)
- `last_active > Current date/time + days: -90` ← 90일 미접속 유령 제외 (2026-06-29 추가)
- Sort: 거리 오름차순 → last_active 내림차순

### 전체 데이터 흐름
```
[Page is loaded] → generate_daily_cards_v5 스케줄 (오늘 카드 없을 때만)
[버블 백엔드] → today_cards 150명 생성 + today_match_done="no"
[Every time] today_cards:count>1 AND today_match_done is no
  → Run JS → Vercel /api/match → bubble_fn_match_result
[JTB B event] → today_cards 교체(상위2) + today_match_done="yes"
```
