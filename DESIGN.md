# 페이서스 디자인 원칙

## 핵심 방향
**화이트 베이스 + 블랙 미니멀 폰트가 기본. 다크모드(블랙 베이스)도 가능.**
어느 쪽이든 최소한의 색, 미니멀 지향.

색은 의미가 있을 때만 쓴다. 많을수록 어지럽고, 적을수록 명확해진다.

---

## 색상 체계

### 기본 팔레트
- **배경**: `#000000` 순수 블랙
- **텍스트**: `#ffffff` 화이트
- **서브 텍스트**: `rgba(255,255,255,0.6~0.85)` opacity로 위계
- **구분선**: `rgba(255,255,255,0.1)` 은은하게

### 브랜드 컬러 (1개만)
- **브랜드 블루**: `#3764f1` — 고정 UI 요소 (로고, 링크, 배지, 팁 날짜 등)

### 동적 컬러 (상태 표현용)
점수/상태처럼 "오늘 컨디션"을 전달할 때만 색이 바뀐다.
- `#3764f1` 파랑 — 최적 (85+)
- `#f59e0b` 앰버 — 무난 (60~84)
- `#f97316` 오렌지 — 주의 (40~59)
- `#fe374f` 레드 — 위험 (~39)

### 위험/경고 컬러
- **레드**: `#fe374f` — 진짜 긴급 상황에만 (에러, 극한 경보)
- stat 태그, 라벨 등 일반 UI에는 절대 red 사용 금지

---

## 위계 원칙

**한 화면에 시선을 끄는 요소는 1개.**

점수(또는 핵심 수치)가 전체 분위기를 잡고, 나머지는 조용히 정보만 전달한다.

```
점수/상태 → 색으로 즉각 전달 (동적 컬러)
세부 정보 → 흰색 or 블루, opacity로 위계
라벨/태그 → 작고 조용하게, 색 강조 없음
```

색이 여러 곳에서 각자 의미를 담으면 "어디를 봐야 하지?"가 된다.
한 곳에서 색이 의미를 담으면 시선이 자연스럽게 흐른다.

---

## 적용 규칙

- stat 태그(습함/잔잔/현재 비 등)는 블루 단색, 강도는 opacity로만 표현
- 동일한 정보가 두 곳에 나오면 한 곳을 제거 (중복 금지)
- 새 색상 추가 전 "기존 색으로 표현 불가능한가?" 먼저 질문
- 경고는 색보다 텍스트로 먼저 표현한다

---

## 참고 사례 (2026-06-20 적용)

weather-app stat 태그를 빨강/흰색/파랑 3색 → 블루 단색으로 통일.
이유: 점수 색(동적 컬러)이 이미 전체 심각도를 전달하므로 태그에 추가 색이 불필요했음.
결과: 색 어지러움 해소, 시선 위계 명확해짐.

---

## 마라톤 플랜 미니앱 디자인 (2026-07-01, DESIGN_SYSTEM.md 기반)

### 색상 — 다크 모드 (DESIGN_SYSTEM.md §2 다크 모드)

```
Background:      #000000
Surface:         #111111
Text Primary:    #FFFFFF
Text Secondary:  rgba(255,255,255,0.60)
Text Tertiary:   rgba(255,255,255,0.36)
Border:          rgba(255,255,255,0.10)
Pacers Blue:     #3764F1  ← 최대 2곳만
```

### 타이포그래피 토큰 (DESIGN_SYSTEM.md §3 적용)

| 용도 | 크기 | 굵기 | tracking |
|------|------|------|----------|
| D-day 숫자 hero | 60px+ / `clamp(3.5rem, 16vw, 5rem)` | 900 | -0.03em |
| 훈련 km hero | 48px / `3rem` | 900 | -0.03em |
| PACERS 스플래시 | `clamp(3.8rem, 19vw, 7rem)` italic | 900 | -0.04em |
| Headline | 28px | 700 | -0.01em |
| Label (라벨) | 0.65–0.75rem | 700 | 0.08em uppercase |
| Caption | 12px | 500 | 0.08em uppercase |

**원칙**: 숫자가 디자인이다. 라벨은 숫자 아래 아주 작게. 절대 라벨에 컬러 강조 없음.

### 스플래시 화면 (NRC campaign-tile 원칙)

```
[풀블리드 컬러 러닝 사진 — /images/splash.jpg 정적 파일, R2 랜덤 금지]
[다크 오버레이: linear-gradient 상단 rgba(0,0,0,0.35) → 하단 rgba(0,0,0,0.85)]

레이아웃 (justify-content: space-between):
  ┌─────────────────────────────┐
  │  (사진이 배경 전체를 채움)   │
  │                             │
  │  ← center-left 앵커         │
  │  PACERS                     │  ← hero: clamp(3.8rem, 19vw, 7rem), italic, 900
  │  마라톤 훈련 플랜           │  ← sub: 0.9rem, 600, rgba(255,255,255,0.7), 0.12em
  │  나 자신을                   │  ← tagline: clamp(1.3rem, 5.5vw, 1.8rem), 800
  │  뛰어넘다                    │
  │                             │
  │  [시작하기]                  │  ← pill button: #fff bg + #111 text + border-radius:9999px
  └─────────────────────────────┘
```

**금지**: italic으로 overflow 발생 → `overflow: hidden` 절대 금지. `white-space: nowrap` 사용.

### 대시보드 탭 구조 (3탭)

```
[ 오늘 ] | [ 전체 플랜 ] | [ 마이 ]
          ↑ 다크 pill 하단 플로팅 네비
```

### 오늘 탭 — 섹션 구조

1. 풀블리드 사진 헤더 (200px, `/images/running-bw.jpg`, 흑백)
2. D-day hero 숫자 (`clamp(3.5rem, 16vw, 5rem)`, 900, white, `-0.03em`)
3. 플랜 요약 한 줄 (14px, rgba(255,255,255,0.6))
4. 오늘 훈련 카드 (km hero 48px, 라벨 12px uppercase)
5. 이번 주 캘린더 (완료 ✓, 오늘 원형 테두리, 예정 점)
6. 훈련 완료 버튼 (pill, #fff bg, 56px)

### 버튼 규격 (DESIGN_SYSTEM.md §5)

| 종류 | 배경 | 텍스트 | 높이 | radius |
|------|------|--------|------|--------|
| Primary (다크 화면) | #FFFFFF | #111 | 56px | 28px (또는 9999px pill) |
| Ghost | transparent | white | 56px | 28px |

탭 피드백: `scale 0.96, 150ms, cubic-bezier(0.34, 1.56, 0.64, 1)` (Snappy)

### 모션 (DESIGN_SYSTEM.md §4 적용)

```css
--ease-spring:   cubic-bezier(0.16, 1, 0.3, 1);    /* 기본 진입 */
--ease-snappy:   cubic-bezier(0.34, 1.56, 0.64, 1); /* 버튼 탭 */
--ease-decel:    cubic-bezier(0, 0, 0.2, 1);         /* 바텀시트 */
```

- 카드 진입: `translateY(32px)→0, opacity 0→1, 400ms spring`
- Stagger: 아이템 70ms 간격 (최대 5개)
- D-day 숫자: count-up 800ms decelerate
- 스플래시 텍스트 stagger: brand 150ms → sub 350ms → tagline 500ms → btn 750ms
- 로딩: Skeleton shimmer (spinner 절대 금지)

### 카드 규격 (다크)

```css
background: #111111;
border: 1px solid rgba(255,255,255,0.10);
border-radius: 16px;
padding: 20px;
/* box-shadow 절대 금지 */
```

### 레이아웃

```
좌우 여백: 24px
섹션 상하 간격: 40px
카드 간격: 12px
하단 네비 높이: 64px + safe-area
```
